@title = 'Improving performance of user encrypted data sync'
@author = 'drebs'
@posted_at = '2017-03-3'
@preview_image = '/img/pages/soledad-performance/upload.png'
@more = true

## Performance improvements in synchronization of user encrypted data

One challenge with end-to-end email encryption is synchronization of user data among devices. A user wants to see all the contents of her email box on both her computer and her mobile phone, for example. From the technical perspective, this means that the content has to be stored securely (that is, encrypted) on each device, and also synchronized between devices without anyone other than the user being able to see its contents.

In order to achieve that, the LEAP project started with U1DB, a solution that already provided a correct and working data synchronization protocol, and added end-to-end encryption on top of it. Data is stored encrypted in the client (that is, the user's device) and is re-encrypted before being synchronized with the server (that is, the user's email provider). From the server, data can be further synchronized to the user's other devices. The encryption keys must also be shared among devices so content can be decrypted there.

Problems started to appear when the proof-of-concept faced the real world: hundreds of email messages with hundreds of kilobytes or even megabytes of attachments can be difficult for a computer to process if the download queue and encryption pipeline are not well designed. This problem gets worse in some scenarios such as, for example, the one faced by the [Pixelated Project](https://pixelated-project.org/) which uses the technology developed by LEAP to implement a multi-user web-based email solution.

Since May 2016 we have been working hard to understand and address the difficulties involved in the encrypted synchronization of user data, to increase its speed while lowering memory and CPU usage. At the end of 2016 we implemented some important changes in the synchronization code which will be contained in the next Bitmask release, and some details of which we share with you here.

By fixing some flaws and reworking the data transfer and encryption/decryption pipeline as a whole, we were able to decrease download and upload time substantially. The figure below shows the download time for 3 distinct scenarios that transfer the same amount of data: 20 documents of 500K each, 100 documents of 100K each, and 1000 documents of 10K each. For download, we achieved twice the speed we had before:  
    ![download times](/img/pages/soledad-performance/download.png) 

For upload, on the other hand, the increase was much more striking. The nature of the synchronization algorithm requires that uploaded data is inserted in an orderly manner in the server. When trying different ways to handle that requirement, we ended up having a very slow algorithm that enqueued data to be uploaded in the client, and sent one by one, taking a long time to finish the whole transfer. After reworking that algorithm, we achieved from 2 to 37 times faster upload speeds, depending on the scenario:
    ![upload times](/img/pages/soledad-performance/upload.png) 

By changing the algorithms used to encrypt and decrypt user data, as well as to authenticate the contents (so we know that data has not been tampered with), we also diminished the time taken for cryptographic operations as a whole. The following figures show the encryption/decryption (plus authentication) times for different sizes of raw data:
    ![raw encrypt times](/img/pages/soledad-performance/raw_encrypt.png) 
    ![raw decrypt times](/img/pages/soledad-performance/raw_decrypt.png) 

These were only some of the bits polished to help make it feasible to have end-to-end encrypted user data synchronized among devices. Also, the performance metrics presented here are only the ones related with total synchronization time. More complete results including memory consumption and application responsiveness will follow soon.

We plan to release the next Bitmask Client and LEAP Platform with these and other features, and keep delivering improvements to support easier to use and better privacy tools. 
