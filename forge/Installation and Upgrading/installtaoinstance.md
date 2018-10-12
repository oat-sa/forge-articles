<!--
parent: 'Installation and Upgrading'
created_at: '2016-11-22 16:48:51'
updated_at: '2016-11-22 16:52:01'
authors:
    - 'Gyula Szucs'
tags:
    - 'Installation and Upgrading'
-->

Install a TAO instance
======================

This shell script can be useful to quickly install a new TAO instance into a separate folder. It takes care of running the necessary commands. It even creates a new Apache2 Vhost file and adds the domain to the hosts file.

It requires three arguments:

-   Name of the instance
-   RAW URL of the composer.json of the instance in oat-sa/deploy-test-package on GitHub (or in any other repo)
-   List of the extensions to be installed. For multiple extensions, it needs to be separated by comma

Usages:

     ./installTaoInstance.sh invalsi https://raw.githubusercontent.com/oat-sa/deploy-test-package/nightly/invalsi/composer.json?token=xxx taoCe,taoInvalsi,taoMonitoring

     ./installTaoInstance.sh act https://raw.githubusercontent.com/oat-sa/deploy-test-package/nightly/act/composer.json?token=xxx taoAct

\>h2. Warning\
\>

\>**Only use it in development environment!**


