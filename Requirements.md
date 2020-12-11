
## Dependencies

- PHP must be installed on your operating system (any version, 5 or 7). 

	- Windows machines do not come with a pre-built PHP and Apache Web Server, and therefore will require both. Visit the ['XAMPP']	(https://www.apachefriends.org/index.html) XAMPP website and download the installer for Windows; the XAMPP is a popular PHP development 	environment that is easy to install and configure.

- Clone the [`hd-wallet-derive`](https://github.com/dan-da/hd-wallet-derive) tool.

- intall [`bit`](https://ofek.github.io/bit/) Python Bitcoin library.

	- pip install bit

- install [`web3.py`](https://github.com/ethereum/web3.py) Python Ethereum library.

	- pip install web3


## Installation Instructions for hd-wallet-derive

The hd-wallet-derive library is written in the PHP language; therefore, you will be required to first set up PHP on your machines before installing and then running the hd-wallet-derive library.

- Once the latest version of PHP installed on our machines, we can install the `hd-wallet-derive` library.

<details><summary>Installation</summary>

1. Open a new powershell or gitbash terminal. Windows users **must** open their terminal as administator as follows:


    * Input `C:\Program Files\Git\bin\bash.exe` directly into the system search bar and launch the program as _Administrator_ from the resulting menu. 
    
    * **This step is required or the installation will fail!**

    * <img alt=bash-exe.png src=Images/bash-exe.png height=500>

2. With your terminal open as indicated for your operating system, cd into your `Wallet' folder and run the following code:

    ```shell
      git clone https://github.com/dan-da/hd-wallet-derive
      cd hd-wallet-derive
      curl https://getcomposer.org/installer -o installer.php
      php installer.php
      php composer.phar install
    ```

3. You should now have a folder called `hd-wallet-derive` containing the PHP library!

4. Create a symlink called `derive` for the `hd-wallet-derive/hd-wallet-derive.php` script into the top level project
  directory using the command: `ln -s hd-wallet-derive/hd-wallet-derive.php derive`

- This will clean up the command needed to run the script in our code, as we can call `./derive`
  instead of `./hd-wallet-derive/hd-wallet-derive.php`.

- Test that you can run the `./derive` script properly, use one of the examples on the repo's `README.md`

The directory structure should look something like this:

![directory-tree](Images/tree.png)

</details>

<details><summary>Verification</summary>

1. Run the command to `cd` in your `hd-wallet-derive` folder.

2. Once you've confirmed your are in your `hd-wallet-derive` folder, execute the following command:

    ```shell
    ./hd-wallet-derive.php -g --key=xprv9tyUQV64JT5qs3RSTJkXCWKMyUgoQp7F3hA1xzG6ZGu6u6Q9VMNjGr67Lctvy5P8oyaYAL9CAWrUE9i6GoNMKUga5biW6Hx4tws2six3b9c --numderive=3 --preset=bitcoincore --cols=path,address --path-change
    ```

3. If installation was successful, you should see output similar to what you see in the following image:

   <img alt=hd-wallet-derive-execute src=Images/hd-wallet-derive-execute.png width=700>

</details> 
<br>

The `hd-wallet-derive` library should now be working and good to go!
