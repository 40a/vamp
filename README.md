![Build status](https://ci.appveyor.com/api/projects/status/s7a7aos4yo2v3vvd?svg=true)

# vamp
A Powershell tool that allows Powershell DSC MOF files to be generated from yaml. 

## Quickstart
* git clone this repository
* create your DSC configuration in a .yml file and place it in configs folder
* edit the vampspec.yml file to contain your target nodes and config names
* Import-Module .\vamp.psm1
* run ```vamp -generate```

## Parameters

* *prep*
  - The 'prep' switch parameter calls the vamp tool to gather to required modules from the configuration .yml files in the configs directory. Once gathered it will then attempt to download
  those modules from the PSGallery and copy them out to any of the nodes that will require them for the upcoming configuration. Since the prep parameter attempts to copy the required modules to the destination nodes it requires that you have access to these nodes.

* *generate*
  - The 'generate' switch parameters calls the vamp tool to generate the mof files required for the configuration defined within the .yml files residing in the configs directory.

* *apply*
  - The 'apply' switch parameters calls the vamp tool to apply the newly generated .mof files to the nodes.


## Version

 * 0.1.0 - Released with basic usability (3rd July 2016)
 * 0.1.1 - Added the ability to have multiple config (.yml) files in the config folder which get compiled into a single mof at runtime. (4th July 2016)
 * 0.1.2 - Reworked the way the Yaml file works - More composite based now in that multiple nodes can be specified and multiple configs for multiple nodes (See examples). (7th July 2016) 

## In the works

 * VScode extension for resource snippits.
 * See Issues tab for other upcoming changes.


## Example 

```yaml
-  feature:
    ResourceID : '[WindowsFeature]webserver'
    name: Web-Server
    ensure: present
    ModuleName : PsDesiredStateConfiguration
    ModuleVersion : 1.0

-  feature:
    ResourceID : '[WindowsFeature]ASPnet45'
    name: Web-Asp-Net45
    ensure: present
    ModuleName : PsDesiredStateConfiguration 
    ModuleVersion : 1.0

-  MSFT_xWebsite:
    ResourceID : '[xWebsite]DefaultSite'
    PhysicalPath: C:\\inetpub\\wwwroot
    state: Started
    Name: 'Default Web Site'
    ensure: present
    ModuleName : xWebAdministration
    ModuleVersion : 1.12.0.0
    DependsOn : '[WindowsFeature]webserver'

-  file:
    ResourceID : '[File]webcontents'
    Type: File
    Contents : 'Testing Complete'
    recurse : true
    ensure: present
    Attributes :
      - Archive
      - ReadOnly
    DestinationPath: C:\\inetpub\\wwwroot\\index.html
    ModuleName : PsDesiredStateConfiguration
    ModuleVersion : 1.0
    DependsOn : '[xWebsite]DefaultSite'
```

#### Example vampspec.yml
```yaml
-  nodes:
    name : 
     - WIN-FileServer

   configs:
    name : 
     - BasicExample

-  nodes:
    name : 
     - WIN-WebServer0
     - WIN-WebServer1

   configs:
    name : 
     - AnotherExample
     - YetAnotherExample

```
