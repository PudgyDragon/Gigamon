<h1>Gigamon Upgrade Guide</h1>
<p>Quick little guide to help you get around having to run the fetch commands for the image files. Guide assumes that you have downloaded the images files 
on your workstation and have them readily available to use.</p>
<h3>NOTE: WinSCP or similar must be installed on your workstation.</h3>

<h2>Fabric Manager</h2>
<p>I won't lie, I felt like an idiot looking at the documentation thinking I had to run the fmctl commands to fetch the image from an external server 
only to realize I was blind and completely missed that the FM is considered its own "Internal Server" and you can upload .img files directly to it. For 
reference, here is a similar upgrade guide you can skim through</p>
<ul>
  <li>https://docs.gigamon.com/pdf-68/Content/Resources/PDF%20Library/GV-6800-Doc/GigaVUE-FM-InstallUpgradeGuide-v68.pdf</li>
</ul>

<h3>Upload Image</h3>
<p>Upgrading the FM is pretty simple and straightforward and can all be done from the GUI. Once you're logged into the FM, navigate to</p>
<pre><code>
  Settings Cog > System > Images > Internal Image Files > Upload
  
</code></pre>
<p>Upload your FM .img file here (this is also where you upload .img files for your nodes if they are connected to your FM). Once it's finished uploading, 
you're ready to start the upgrade.</p>

<h3>Upgrade</h3>
<p>From anywhere on the FM, select the profile icon on the top right of the GUI and select "Upgrade". From the next interface, do the following</p>
<ul>
  <li>Select Image Server Type - Internal Image Server</li>
  <li>Select Version - FM .img file</li>
  <li>Start Upgrade - Click the Start Upgrade button</li>
</ul>
<p>The FM will start to do its thing. Leave the page open and don't refresh. When the upgrade is complete, the page will tell you the device is finished 
and prompt you to go to the dashboard.</p>

<h2>Nodes</h2>
<p>In the event that your device can't be connected to your Fabric Manager for some reason, there's a way to use the CLI to update it to the latest versions 
without using the fetch commands. You won't find the hidden file in the official documentation, but for the rest of the guide you can have this for reference</p>
<ul>
  <li>https://docs.gigamon.com/pdfs/Content/Resources/PDF%20Library/GV-6300-Doc/GigaVUE-OS-UpgradeGuide-v63.pdf</li>
</ul>

<h3>WinSCP</h3>
<p>Begin by authentication to the device with WinSCP (or whatever program you use for transfering files). On the Gigamon side, 
either click the folder button labeled "Open directory/bookmark" or use the shortcut</p>
<pre><code>
  Ctrl + O
  
</code></pre>
<p>This will allow you to directly navigate to the folder you need to drop the .img file into. You do it this way because the default account doesn't have 
the proper permissions to access the /var folder. The directory you need to enter is</p>
<pre><code>
  /var/opt/tms/images
  
</code></pre>
<p>You <i>shouldn't</i> have any other image files in here, but in the event that you do you can delete them from the WinSCP pane. Place your new Gigamon 
.img file in here.</p>

<h3>CLI</h3>
<p>Once the file is staged in the images folder, from the CLI you can follow the normal guide for upgrading the device. For my fellow lazy people out there, 
the order of commands are as follows</p>
<pre><code>
  image install gigamon.img
  image boot next
  write mem
  reload
</code></pre>
<p>This will install the image, designate which image to use during the next reboot, write the config to memory, and reboot (reload) the device. If this is a 
brand new device, the reboot is pretty quick since there aren't any crazy configurations on it.</p>

<h2>Cake</h2>
<p>Easy peasy lemon squeezy.</p>
