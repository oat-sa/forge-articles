
<!--
created_at: '2018-09-19 16:52:00'
tags:
    - 'Developer Guide'
    - 'Installation and Upgrading'
-->

# Update translations in TAO with a translation package from Pootle

All these steps need to be run from the TAO root directory. This tutorial will work with translations coming from the TAO 3.2 project in Pootle and for an overall update in TAO 3.2/3.3 but may require some changes for future versions.

Let xx-XX the locale name to update and assuming it already exists. If not, to create that latter please apply step 1.
_Note:_ In this tutorial the term *locale* - language spoken in a specific region/country - is often referred to as a *language*.

1. (Optional) In case the locale doesn't exist yet in your installation, the following will create all original .po files for the new locale named as xx-XX and will do this for all TAO extensions specified:
    `sudo -u www-data php tao/scripts/taoTranslate.php -v -a create -l xx-XX -ll '[locale label]' -u=[login] -p=[password] -e [extension1,extension2,...]`

   _Note:_ for TAO 3.2, [extension1,extension2,...] can be replaced by the following list of extensions taken from the community version (are excluded the few extensions not requiring any translation): tao,ltiDeliveryProvider,taoGroups,taoMediaManager,taoTests,taoCe,taoItems,taoOutcomeUi,taoTestTaker,taoDelivery,taoQtiItem,taoDeliveryRdf,taoLti,taoQtiTest,taoClientDiagnostic,funcAcl,pciSamples,qtiItemPci,qtiItemPic,taoBackOffice,taoEventLog,taoOpenWebItem,taoProctoring,taoResultServer,taoRevision,taoTestLinear,taoDacSimple

   _Remark:_ A small typo in the tao core extension may lead in the new locale to be shown in the list of available languages in the back-office but being absent in the list of interface and item languages of a test-taker account. Here is a workaround for this bug, fixed in tao-core since version 19.17.2:
	`sudo -u www-data sed -ir "s/<tao:languageUsages/<tao:LanguageUsages/g" tao/locales/xx-XX/lang.rdf`

   The following will add support for the new locale and make it available as a new interface and data language:

    sudo -u www-data php tao/scripts/taoRDFImport.php -v -n -u=[login] -p=[password] http://www.tao.lu/Ontologies/TAO.rdf# -i tao/locales/xx-XX/lang.rdf 
    * Running tao/scripts/taoRDFImport.php
    Connecting to TAO as '[login]' ...
    Connected to TAO as '[login]'.
    Successfully imported 6 tripples

2. Copy the export package provided by the Pootle administrator in the TAO root folder and the two shell scripts to ease the reporting of translations into TAO.
Extract the content of the export .zip package:
	`unzip xx-XX-tao32_YYYYMMDD_HHMM.zip .`
This will create a subfolder named as 'xx-XX-tao32' in the TAO root directory.

3. Run this script to drop every .po file from the source folder to the different locale folders of each TAO extension.
	`sudo ./pootle_export_to_tao.sh tao32 xx-XX .`

4. If you aim to report all translations from your contribution while keeping all new source strings or removing old ones, you will need to merge the recent changes coming from your TAO version against the copied translation files. The second part will trigger recompilation in order to merge the latest translations into TAO.
	`sudo ./merge_all_translations.sh xx-XX`

   _Notes:_
   - In case some translations need reworking, you may edit the translation files manually in the folder tree 'xx-XX-tao32/xx-XX/tao32/' then replay step 3 as many times as required.
   - Having the default login page using the same locale requires the following to be added in ./config/generis.conf.php:
	`define('DEFAULT_ANONYMOUS_INTERFACE_LANG','xx-XX');`

5. (Cleanup) Remove the export .zip package, the shell scripts and the extracted folder ('xx-XX-tao32').

6. (Backup) To keep an archive of this you may create a package containing all generated translations files:
	`zip -r9 tao_locales_xx-XX.zip */locales/xx-XX */views/locales/xx-XX/`