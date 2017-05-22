@title = 'Virtual Machines'
@summary = "Running LEAP platform on remote virtual machines"

Introduction
------------------

You can use the `leap` command line to easily manage remote virtual machines.

Note: there are two types of virtual machines that `leap` can handle:

* **Local** virtual machines running with vagrant, for use in testing.
* **Remote** virtual machines hosted by a cloud provider like AWS or Rackspace.

This guide is for "remote virtual machines". For "local virtual machines" see [[vagrant]].

Currently, only Amazon AWS is supported as a cloud provider.

Configuration
---------------------

To get started with virtual machines, you must configure a `cloud.json` file with your API credentials for the virtual machine vendor. This file lives in the root of your provider directory.

For example:

    {
      "my_aws": {
        "api": "aws",
        "vendor": "aws",
        "auth": {
          "region": "us-west-2",
          "aws_access_key_id": "xxxx my key id xxxx",
          "aws_secret_access_key": "xxxx my access key xxxx"
        }
      }
    }

This will configure a cloud "authentication profile" called "my_aws". This profile will be used by default if there is only one. See below for managing multiple authentication profiles.

*Required cloud.json properties*

* `$profile`: In this case, 'my_aws'.
* `$profile.api`: For now, must always be "aws".
* `$profile.vendor`: For now, must always be "aws".
* `$profile.auth`: API specific authentication configuration for this profile. In the case of AWS, it must include `auth.region`, `auth.aws_access_key_id`, and `aws_secret_access_key`.

*Additional cloud.json properties*

In addition to required configuration properties, these are optional:

* `$profile.default_image`: What image to use for new nodes by default. Generally, you should not specify this, because it will automatically select the right Debian image for your region. A node can override this with the property `vm.image`.
* `$profile.default_options`: This is passed directly to the cloud API, and so is specific to whichever API you are using. The node can override this with the property `vm.options`.

A more complete example `cloud.json`:

    {
      "my_aws": {
        "api": "aws",
        "vendor": "aws",
        "auth": {
          "region": "us-west-2",
          "aws_access_key_id": "xxxx my key id xxxx",
          "aws_secret_access_key": "xxxx my access key xxxx"
        },
        "default_image": "ami-98e114f8",
        "default_options": {
          "InstanceType": "t2.nano"
        }
      }
    }

See also:

* [[Available instance types for AWS => https://aws.amazon.com/ec2/instance-types/]]
* [[Where’s My Secret Access Key? => https://aws.amazon.com/pt/blogs/security/wheres-my-secret-access-key/]]

Usage
--------------------------------------------

See `leap help vm` for a description of all the possible commands.

In order to be able to create new virtual machine instances, you need to register your SSH key with the VM vendor.

    leap vm key-register

You only have to do this once, and only people who will be creating new VM instances need to do this.

Once you have done that, you just `leap vm add` to create the virtual machine and then `leap vm start` to actually boot it.

    leap vm add mynode
    leap vm start mynode

You can specify seed values to `leap vm add`. For example:

    leap vm add mynode services:webapp tags:seattle vm.options.InstanceType:t2.small

For an email provider installation, you should specify the following seeds:

    leap vm add mynode services:webapp,couchdb,soledad,mx

Check to see what the status is of all VMs:

    leap vm status

If it looks good, you can now deploy to the new server:

    leap node init mynode
    leap deploy mynode

To stop the VM:

    leap vm stop mynode

To destroy the VM and clean up its storage space:

    leap vm rm mynode

In general, you should remove VMs instead of stopping them, unless you plan on stopping the VM for a short amount of time. A stopped VM will still use disk space and still incur charges.

Keeping State Synchronized
-------------------------------------------------

The LEAP platform stores all its state information in flat static files. The virtual machine vendor, however, also has its own state. 

On the provider side, VM state is stored in node configuration files in `nodes/*.json`. Of particular importance are the properties `ip_address` and `vm.id`. 

Most of the time, you should not have any trouble: the `leap vm` commands will keep things in sync. However, if the state of your configuration files gets out of sync with the state of the virtual machines, it can cause problems.

The command `leap vm status` will warn you whenever it detects a problem and it will usually propose a fix.

Typically, the fix is to manually update the binding between a local node configuration and the running remote virtual machine, like so:

    leap vm bind NODE_NAME VM_ID


Multiple authentication profiles
---------------------------------------------

If you have multiple profiles configured in `cloud.json`, you can specify which one you want to use:

* Set the `vm.auth` property in the node configuration to match the name of the authentication profile.
* Or, pass `--auth PROFILE_NAME` on the command line.
