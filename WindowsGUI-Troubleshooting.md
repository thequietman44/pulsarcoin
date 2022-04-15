# Windows GUI Wallet Troubleshooting Guide

Below are some of the most common troubleshooting steps you may be asked to do if you're having issues with your wallet. The two most important are knowing how to [Back Up Your Wallet](#back-up-your-wallet) and how to [Restore Your Wallet](#restore-your-wallet) so you don't lose access to your funds.

## Back Up Your Wallet

**NOTE**: It's recommended that you encrypt your wallet. Unencrypted wallet files can be opened by anyone or anything with access to your system or backup media. See the [Windows GUI Wallet Setup guide](WindowsGUI-WalletSetup.md) for instructions.

1. In the Pulsar wallet go to File > Backup wallet...

    <img src="images/win-gui-backupmenu.png" width="200">

2. Choose a location to save your backup file.

    <img src="images/win-gui-backupsave.png" width="600">

3. You should see a notification confirming that your wallet was backed up.

    <img src="images/win-gui-backupnotify.png" width="300">

**NOTE**: It's best practice to keep multiple copies of your backup file on secure, removeable storage to ensure you do not lose access to your PLSR as a result of hard disk failure.

## Clear All Pulsar Data

**NOTE**: If you installed the Pulsar Wallet using the setup.exe with default settings, your Pulsar data should be in `%AppData%\Pulsar`. If you used `pulsar-qt.exe` or installed with custom settings your wallet and blockchain data may be in a different location.

1. Quit the Pulsar wallet app if it's running. 

2. Click the Windows Start, type `%AppData%\Pulsar`, and hit Enter.

    <img src="images/win-gui-appdatastart.png" width="250">

3. You should see Pulsar config files and a few folders.

    <img src="images/win-gui-appdata.png" width="600">

4. Select all files/folders and delete them.

    <img src="images/win-gui-appdatadelete.png" width="600">

5. Launch the Pulsar wallet app to start syncing blockchain data again.

## Restore Your Wallet

1. Quit the Pulsar wallet app if it's running.

2. Click the Windows Start, type `%AppData%\Pulsar`, and hit Enter.

    <img src="images/win-gui-appdatastart.png" width="250">

3. You should see Pulsar config files and a few folders. (If not, start the Pulsar wallet app and let it sync the blockchain, then quit, and check again.). Double-click the `wallets` folder.

    <img src="images/win-gui-appdatawallet.png" width="600">

4. Drag your backup wallet file to the `wallets` folder (hold `Ctrl` to make a copy instead of moving the original).

    <img src="images/win-gui-walletmove.png" width="600">

5. Delete `wallet.dat` and then rename your backup file to `wallet.dat`.

    <img src="images/win-gui-walletdelete.png" width="600">

    <img src="images/win-gui-walletrename.png" width="600">

6. Start the Pulsar wallet app again and you should see your old wallet balance and addresses.
