@title = "webapp"
@summary = "leap_web user management application and provider API."

Introduction
------------------------

The service `webapp` will install the web application [[leap_web => https://leap.se/git/leap_web.git]]. It has performs the following functions:

* REST API for user registration and authentication via the Bitmask client.
* Admin interface to manage users.
* Client certificate distribution and renewal.
* User support help tickets.

Coming soon:

* Billing.
* Customizable and localized user documentation.

The leap_web application is written in Ruby on Rails 3, using CouchDB as the backend data store.

Topology
-------------------------

Currently, the platform only supports a single `webapp` node, although we hope to change this in the future.

* `webapp` nodes communicate heavily with `couchdb` nodes, but the two can be on separate servers.
* The `monitor` service, if enabled, must be on the same node as `webapp`.

Configuration
--------------------------

Essential options:

* `webapp.admin`: An array of usernames that will be blessed with administrative permissions. These admins can delete users, answer help tickets, and so on. These usernames are for users that have registered through the webapp or through the Bitmask client application, NOT the sysadmin usernames lists in the provider directory `users`.

Other options:

* `webapp.engines`: A list of the engines you want enabled in leap_web. Currently, only "support" is available, and it is enabled by default.

For example, `services/webapp.json`:

    {
      "webapp": {
        "admins": ["joehill", "ali", "mack_the_turtle"]
      }
    }

By putting this in `services/webapp.json`, all the `webapp` nodes will inherit the same admin list.

There are many options in `provider.json` that also control how the webapp behaves. See [[provider-configuration]] for details.

Invite codes
-------------------

The invite code functionality will require new users to provide a valid invite code while signing up for a new account. This is turned on by default since platform version 0.10. When switching it off, anyone will be able to create a new account.

Because even the first (admin) user that registers needs to have an invite code at hand, you´ll have to generate one:

    workstation$ leap run invite
      = [bumblebee] running `cd /srv/leap/webapp; RAILS_ENV=production bundle exec rake "generate_invites[1,1]"`
      = [bumblebee] pgae-aaub
      = [bumblebee] complete in 5.031s.

Where `bumblebee` should be replaced with the name of your webapp node. You can now browse to https://example.com (replace with your domain) and register your first user, by using the invite code you just generated. If you added your user as an admin user (see above), you can now also generate new invite codes from within the web application.

It is possible to specify both **NUM**, the amount of codes to generate and **USES**: an optional parameter: by default all new invite codes can be used once and will then become invalid. If you provide another value, you can set how often it can be used before they're invalidated. To generate 2 codes that can be both reused 3 times you can run this:

    workstation$ leap run invite 2,3 prodcution
      = [bumblebee] running `cd /srv/leap/webapp; RAILS_ENV=production bundle exec rake "generate_invites[2,3]"`
      = [bumblebee] pgae-aaub
psau-2qwbs
      = [bumblebee] complete in 5.031s.

If you want to open up registration to the world, you can set the `enrollment_policy` option to `open` in `provider.json`:

    {
      "enrollment_policy": "open"
    }

This only works with LEAP platform 0.8 or higher. The default enrollment policy changed from open to invite with the platform 0.10.

Run `leap deploy` to disable the option.

Customization
---------------------------

The provider directory `files/webapp` can be used to customize the appearance of the webapp. All the files in this directory will get sync'ed to the `/srv/leap/webapp/config/customization` directory of the deployed webapp node.

Files in the `files/webapp` can override view files, locales, and stylesheets in the leap_web app:

For example:

    stylesheets/ -- override files in Rails.root/app/assets/stylesheets
      tail.scss -- included before all others
      head.scss -- included after all others

    public/ -- overrides files in Rails.root/public
      favicon.ico -- custom favicon
      img/ -- customary directory to put images in

    views/ -- overrides files Rails.root/app/views
      home/
        index.html.haml -- this file is what shows up on
                           the home page
      pages/
        privacy-policy.en.md -- this file will override
                                the default privacy policy
        terms-of-service.en.md -- this file will override
                                  the default TOS.

    locales/ -- overrides files in Rails.root/config/locales
      en.yml -- overrides for English
      de.yml -- overrides for German
      and so on...

To interactively develop your customizations before you deploy them, you have two options:

1. Edit a `webapp` node. This approach involves directly modifying the contents of the directory `/srv/leap/webapp/config/customization` on a deployed `webapp` node. This can, and probably should be, a "local" node. When doing this, you may need to restart leap_web in order for changes to take effect (`touch /srv/leap/webapp/tmp/restart.txt`).
2. Alternately, you can install leap_web to run on your computer and edit files in `config/customization` locally. This approach does not require a provider or a `webapp` node. For more information, see the [leap_web README](https://github.com/leapcode/leap_web).

NOTE: If you add a `tails.scss` or `head.scss` file, then you usually need to run `rake tmp:clear` and restart rails in order for the new stylesheet to get recognized. You should only need to do this once.

Once you have what you want, then copy these files to the local provider directory `files/webapp` so that they will be installed each time you deploy.

Customization tutorial
----------------------------

This mini-tutorial will walk you through creating a custom "branding" of the leap_web application. We will be creating a provider called "Prehistoric Computer."

Here are the files we are going to create:

    leap_web/config/customization
    ├── locales
    │   ├── en.yml
    │   └── es.yml
    ├── public
    │   ├── favicon.ico
    │   └── img
    │       └── masthead.png
    ├── stylesheets
    │   └── tail.scss
    └── views
        └── pages
            ├── privacy-policy.en.md
            └── privacy-policy.es.md

All these files are available in the source code in the [[customization.example => https://github.com/leapcode/leap_web/tree/develop/config/customization.example]] directory.

Remember, these files may live different places:

* `user@localmachine$ leap_web/config/customization`: This will be the path if you have checked out a local copy of leap_web.git and are running `rails server` locally in order to test your customizations.
* `user@localmachine$ PROVIDER/files/webapp`: This is the local provider directory where the files should be put so that they get correctly deployed to webapp nodes.
* `root@webappnode# /srv/leap/webapp/config/customization`: This is where the files in the local provider directory `PROVIDER/files/webapp` get copied to after a `leap deploy` to a live webapp nodes.

### Override translations

You can add additional locale files in order to change the text used in the existing application and to add translations for string that you added to the application.

In this example, we will be altering the default text for the "login_info" string. In `config/locales/en/home.en.yml` there is this entry:

    en:
      login_info: "Log in to change your account settings, create support tickets, and manage payments."

We are going to override this with some custom text in English and Spanish:

`leap_web/config/customization/locale/en.yml`:

    en:
      login_info: Authenticate to change your "Prehistoric Computer" settings.

`leap_web/config/customization/locale/es.yml`:

    es:
      login_info: Autenticar a cambiar la configuración de "Computer Prehistoria."

Now, the home page of leap_web will use these new strings instead of the default. Remember that you must restart rails in order for new locale files to take effect.

### Override static pages

You can also override any of the static files included with leap_web, such as the privacy policy or terms of service.

Here is how we would create a custom privacy policy in English and Spanish:

`leap_web/config/customization/views/pages/privacy-policy.en.md`:

    # Custom Privacy Policy
    This is our privacy policy.

`leap_web/config/customization/views/pages/privacy-policy.es.md`:

    # Custom Política de Privacidad
    Esta es nuestra política de privacidad.

### Add a custom header

Now we will add a custom header to every page. First, we add the images:

    leap_web/config/customization
        ├── public
            ├── favicon.ico
            └── img
                └── masthead.png

You can create your own, or use the example files in https://github.com/leapcode/leap_web/tree/develop/config/customization.example

Now, we add some custom CSS so that we can style the masthead:

`leap_web/config/customization/stylesheets/tail.scss`

    $custom-color: #66bbaa;

    a {
      color: $custom-color;
    }

    //
    // MASTHEAD
    //

    #masthead {
      background-color: $custom-color;
      border-bottom: none;

      // make the masthead clickable by replacing the
      // site name link with the masthead image:
      .title {
        padding: 0px;
        .sitename a {
          display: block;
          background: url(/img/masthead.png) 0 0 no-repeat;
          font-size: 0px;
          height: 100px;
          background-size: auto 100px;
        }
      }
    }

    // make the home page masthead slightly larger
    body.home #masthead {
      .sitename a {
        height: 150px;
        background-size: auto 150px;
      }
    }

    //
    // FOOTER
    //

    #footer .links {
      background-color: $custom-color;
    }

NOTE: If you add a `tails.scss` or `head.scss` file, then you usually need to run `rake tmp:clear` and restart rails in order for the new stylesheet to get recognized. You should only need to do this once.


Custom Fork
----------------------------

Sometimes it is easier to maintain your own fork of the leap_web app. You can keep your customizations in that fork instead of in the provider `files/webapp` directory. Or, perhaps you want to add an engine to the application that modifies the app's behavior.

To deploy your own leap_web, modify the provider file `common.json`:

    {
      "sources": {
        "webapp": {
          "revision": "origin/develop",
          "source": "https://github.com/leapcode/leap_web",
          "type": "git"
        }
      }
    }

To target only particular environment, modify instead `common.ENV.json`, where ENV is the name of the environment.

See https://github.com/leapcode/leap_web/blob/develop/doc/DEVELOP.md for notes on getting started hacking on leap_web.

Maintenance mode
------------------

You can put the webapp into maintenance mode by simply dropping a html file to `/srv/leap/webapp/public/system/maintenance.html`. For example:

    workstation$ leap ssh webappnode
    server# echo "Temporarily down for maintenance. We will be back soon." > /srv/leap/webapp/public/system/maintenance.html

Known problems
---------------------------

* Client certificates are generated without a CSR. The problem is that this makes the web
  application extremely vulnerable to denial of service attacks. This was not an issue until we
  started to allow the possibility of anonymously fetching a client certificate without
  authenticating first.
* By its very nature, the user database is vulnerable to enumeration attacks. These are
  very hard to prevent, because our protocol is designed to allow query of a user database via
  proxy in order to provide network perspective.
