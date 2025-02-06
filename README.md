# Background

This repository is a manifest application debug/demo/troubleshooting projects

This approach allows new developers to setup an application with minimal effort.  The manifest file will pull in a copy of zephyr and another libraries/repos specified in the west.yml.

Some background on Zephyr workspaces and west manifest files.

https://docs.zephyrproject.org/latest/develop/west/basics.html

https://docs.zephyrproject.org/latest/develop/application/index.html

https://blog.golioth.io/improving-zephyr-project-structure-with-manifest-files/


## Setup

### 0)  python .venv

If you installed zephyr w/ the MCUXpresso installer, you need to activate the python .venv before running commands from the termimal.


#### Windows		  

`%userprofile%/.mcuxpressotools/.venv/Scripts/activate.bat`

#### Linux

`source ~/.mcuxpressotools/.venv/bin/activate`

### 1) Create a working folder on your local machine i.e. 'zephyr-debug'

### 2) cd into that folder and run:


```
west init -m https://github.com/nxp-pro-support/zephyr_debug --mr main
```

This initializes the folder as a west/zephyr workspace registered to our application repository.

![westinit](https://github.com/user-attachments/assets/e8ef3847-9884-4891-b699-e43253df9339)

### 3)  Run `west update`.   

This may take several minutes to pull in all of the dependencies. In this step,  west will look at the manifest and pull down all the dependencies.   In this case, the dependecies are the vanilla Zephyr repository and our sample modules/library repository. It is quite large and can take several minutes but only has to be initialized once.  Future calls to west update are much quicker.

![zephyr_start](https://github.com/nxp-pro-support/zephyr-start/assets/152433281/8df1b0aa-721d-4895-a4ae-12a2d6c6ff4d)


### 4)  west build/west flash

Now you can build samples in the zephyr_debug folder the common way.  There is a hello_zephyr project in the *applications* folder.  The workspace will also have a copy of the zephyr source tree with the samples.

i.e.  (for a pristine build)

` west build -bmimxrt1170_evk@B/mimxrt1176/cm7 --pristine`

then

`west flash`

or

`west flash -r jlink`

if you have a jlink or the EVK/FRDM board is programmed as a jlink


### 5)  Debug with Segger Ozone

In this workflow, I show using Segger Ozone to debug an application after a build.   

https://www.segger.com/products/development-tools/ozone-j-link-debugger/

Segger Ozone is a stand alone debugger that works with a j-link (or the EVK’s programmed as a J-Link).   It is a popular option in the Zephyr community.   It has many features that are not included in other debuggers such as live variable viewing and plotting data real-time.

Inside of the “hello_world” application is a file *RT1050.jdebug* which is already configured to point to the output of the hello zephyr build:

*zephyr-start/hello-world/build/zephyr/zephyr.elf*

There are some .jdebug files for a few other targets as well.

![debug_w_ozone](https://github.com/nxp-pro-support/zephyr-start/assets/152433281/a989f9bd-2523-4e7b-8b55-adedeb7094d7)

***Important note for flashless MCUs***

The i.MX RT series parts are flashless with a ROM bootloader.   There are a couple of extra steps to create a ozone project. 

You can use the RT1050.jdebug as a template to copy to other example folders. In the future, I can show you how create the jdebug from the ozone new project wizard.

You should be able to copy it to a folder of another project and it will point to the build/zephyr/zephyr.elf in the application folder.

**Example:**

Here I copy the RT1050.jdebug to the **samples/drives/uart/echo_bot** project and then build/run.

![debug_uart_driver_sample](https://github.com/nxp-pro-support/zephyr-start/assets/152433281/0e4389b4-df8a-4205-a49e-6a86e8a922b4)

Link to Segger note about ozone debug w/ rom bootloader:

https://wiki.segger.com/Debug_on_a_Target_with_Bootloader

'ROM bootloader

Should your setup be that you have a bootloader in ROM that needs to be executed first simply leave the functions AfterTargetDownload() and AfterTargetReset() empty (but not commented out!). This will override Ozone's default and nothing will be executed so the ROM bootloader can run without interference and jump to the application space where per default Ozone will then stop at main.'



