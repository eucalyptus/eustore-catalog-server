Eustore Catalog Server
======================

These are the configuration files needed to create a local eustore server that can be used with the eustore client tools.

## Pre-reqs

The requirements for the eustore-catalog-server are as follows:

* An apache/nginx server
* (Optional) DNS entry for eustore-catalog-server (i.e. the value for "EUSTORE_URL") that can be used with the eustore client tools

## Web server setup

The following is an example of an apache configuration to be used to set up the eustore-catalog-server:

```
<VirtualHost *:80>
    ServerAdmin webmaster@eucalyptus.com
    ServerName emis.eucalyptus.com
    DocumentRoot /srv/images/
    ErrorLog logs/emis.eucalytpus.com_error.log
    CustomLog logs/emis.eucalytpus.com_access.log common
</VirtualHost>
```

## Catalog JSON file

The catalog JSON file is the connection between the eustore client tools and the eustore-catalog-server. This repo contains a template file - catalog-eustore - which can be used to create a custom catalog-eustore file.  The attributes of catalog-eustore template are as follows:

* url => public URL for the image
* recipe => what OS the image is based off of
* contact => user/group responsible for maintaining the image
* stamp => unique stamp for the image; calculated by the following command - date +%s|md5sum|sed '1,$ s/\(....\)\(....\).*/\1-\2/'
* version => what category the image is part of -> partner-images or experimental-images
* architecture => architecture -> x86_64, i386, i686
* single-kernel => if the image can be run on multiple hypervisors with one kernel
* hypervisors-supported => hypervisors image supports
* date => calculated by the following command - date "+%Y%m%d%H%M%S"
* os => the OS the image 
* description => brief description of the image

After populating the file with the appropriate information, use the update_catalog.py to create a catalog file.

update_catalog.py catalog-eustore > catalog

This adds the "name" attribute to the catalog JSON file, which is used as a checksum once the image is downloaded by the eustore client tool.  The catalog file needs to be at the DocumentRoot definition of your website.   

## Image location and image file format

When the eustore client tool accesses the eustore-catalog-server, it refers to the location of the tar-gzipped file located on the web server from the perspective of the DocumentRoot by referencing the "url" attribute of the image. The tar-gzipped file needs to have the following format:

* image
* <hypervisor>-kernel (where hypervisor is "kvm", "xen", "vmware")
   * vmlinuz file
   * initrd file

*Note: there can be multiple <hypervisor>-kernel directories.

Thats it.  After that, make sure the EUSTORE_URL points to the local eustore server, and everything should be good to go.
