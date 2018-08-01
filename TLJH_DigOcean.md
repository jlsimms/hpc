# Instructions for installing The Littlest JupyterHub (TLJH) on a Digtial Ocean VM

Basic installaion is covered in the [TLJH documentation](https://the-littlest-jupyterhub.readthedocs.io/en/latest/tutorials/digitalocean.html). All subsequent steps unless otherwise noted are run through a terminal launched from within a Jupyter notebook (New->Terminal).

Note that TLJH is configured to allow those with admin privileges on JH to execute `sudo` commands without a password - tread carefully! More information about user accounts and admin rights can be found in the [TLJH Security Considerations documentation](https://the-littlest-jupyterhub.readthedocs.io/en/latest/topic/security.html). General [instructions to install packages](https://the-littlest-jupyterhub.readthedocs.io/en/latest/howto/user-environment.html) can be found on the site as well. A final initial consideration is that when using `pip`, likely you'll want to use the `-E` switch to ensure you are installing for anyone in the environment, rather than just yourself.

Before running anything, however, it is best to update the `apt` repository:

* `sudo apt update`

### NumPy

* `sudo -E pip install numpy`

### Octave Kernel

* Install Octave itself with `sudo apt install octave`
* It is recommended to install `gnuplot` to support inline plotting with Octave, so run `sudo apt install gnuplot`
* Install the actual kernel: `sudo -E pip install octave_kernel`

### R and the R Kernel

* Follow the [basic instructions from Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-install-r-on-ubuntu-18-04) to install R itself
* Installing the packages to provide the kernel to Jupyter is a little more nuanced, because in my experience some packages are missing at the system level. Essentially, the [basic instructions are on the IRkernel GitHub site](https://irkernel.github.io/installation/#source-panel). Essentially, they involve:
	* Launching into an interactive R session from the terminal: `sudo -i R`
	* Then within the R session, running:
		* `install.packages(c('crayon', 'pbdZMQ', 'devtools'))`
		* `devtools::install_github(paste0('IRkernel/', c('repr', 'IRdisplay', 'IRkernel')))`
* But note that there seem to be dependency failures for the `devtools` package. In particular, it seems that `libcurl` and some related dev tools aren't available by default on the VM. The R "error" messages that it throws when you attempt to install a package for which a dependency does not exist are actually super helpful and more or less tell you specifically what to do to correct it, though oddly it doesn't check for those dependencies prior to attempting installation, however it does clean up nicely when it fails. In any case, all dependencies were satisfied by running the following three commands:
	* `sudo apt install libcurl4`
	* `sudo apt install libcurl4-openssl-dev`
	* `sudo apt install libssl-dev`
* Once all dependencies are resolved and the above two R session commands complete with no errors, you need to make the kernel available to Jupyter by running the following command in an R session:
	* `IRkernel::installspec(user = FALSE)`

### nbgrader
Check the [basic instructions](http://nbgrader.readthedocs.io/en/stable/user_guide/installation.html), but basically from a terminal, run:

*  `sudo -E pip install nbgrader`
*  `jupyter nbextension install --sys-prefix --py nbgrader --overwrite`
*  `jupyter nbextension enable --sys-prefix --py nbgrader`
*  `jupyter serverextension enable --sys-prefix --py nbgrader`

### nbgitpuller
Check the [basic instructions](https://github.com/jupyterhub/nbgitpuller), but basically from a terminal, run:

* `sudo -E pip install nbgitpuller`
* `sudo jupyter serverextension enable --py nbgitpuller --sys-prefix`

### Restarting the TLJH Service

* `sudo systemctl restart jupyterhub`