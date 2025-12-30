<h1>Installing TLS Certificates</h1>
<p>This is a guide for installing TLS certificates on both the Gigamon Fabric Manager (FM) and Nodes. The online guide provided by Gigamon wasn't all encompassing. There are other guides online 
that you can reference as well. My guide is a concatenation of guides that ended up working the way it needed to.</p>

<h2>Gigamon Fabric Manager</h2>
<p>The first guide is for the Gigamon FM</p>

<h3>Pre-Requisites</h3>
<h4>Backups</h4>
<p>Login to the CLI of your FM and sudo to root. You will want to make backups of the current certificates on the box in case you make an oopsie</p>
<pre><code>
  cd /etc/pki/tls/certs/
  cp localhost.crt localhost.crt.bak
  cd /etc/pki/tls/private
  cp localhost.key localhost.key.bak
  
</code></pre>

<h4>Certificate Chain</h4>
<p>Using Notepad++ or your preferred text editor, create a .crt file with all of the certificates in your cert chain in the following order</p>
<ul>
  <li>Server (Top)</li>
  <li>Intermediate CA (Middle)</li>
  <li>Root CA (Bottom)</li>
</ul>
<p>Make sure to kep all pieces of the certs, including the "Begin Cert" and "End Cert". You can name this file whatever you want because it will be changed once it's on the box, just make 
sure it's a .crt extension. Once you have this created, SCP it and the .key file onto your FM and place them in the respective directories</p>
<pre><code>
  mv FM.crt /etc/pki/tls/certs/
  mv FM.key /etc/pki/tls/private
  
</code></pre>

<h4>Edit SSL Conf</h4>
<p>Lastly for pre-requisites, edit the following file and save it</p>
<pre><code>
  vi /etc/httpd/conf.d/ssl.conf
      # Below the SSLCertificateFile that references localhost.crt, add
      SSLCertificateChainFile /etc/pki/tls/certs/localhost.crt
  
</code></pre>

<h3>Installation</h3>
<p>Once you have the pre-requisites done, you can move on the this section.</p>

<h4>Replace Old Certs</h4>
<p>With your backups created, you can now replace the old cert and key with your new one and give them the right permissions. The permissions much match what I have below.</p>
<p>IMPORTANT: The cert and key MUST be named localhost.crt and localhost.key, respectively.</p>
<pre><code>
  # Certificate
      cd /etc/pki/tls/certs/
      mv FM.crt localhost.crt
      chmod 644 localhost.crt
  # Key
      cd /etc/pki/tls/private
      mv FM.key localhost.key
      chmod 600 localhost.key
  
</code></pre>

<h4>Concatenate Files</h4>
<p>With the permissions set, concatenate the cert and key into a pem file to allow the load balancer functionality, and for the cert to work</p>
<pre><code>
  cat /etc/pki/tls/certs/localhost.crt /etc/pki/tls/private/localhost.key > /etc/pki/tls/certs/localhost.pem
  
</code></pre>
<p>When I did this, I noticed that the Beginning of the key was on the same line as the End of the certificate. You can simp[ly edit the file, do a return/enter between the two, and save it.</p>


<h4>Restart Services</h4>
<p>There are two services you need to restart, and an extra service you will want to verify is working aftewards</p>
<pre><code>
  # Restart
      systemctl reload haproxy.service
      systemctl restart httpd.service
  # Status Check
      systemctl status tomcat@cmz.service
      systemctl status haproxy.service
      systemctl status httpd.service
</code></pre>

<p>That's all that should need to be done for the FM. The installation for the nodes can be found below.</p>


<h2>Gigamon Nodes</h2>
<p>The nodes will be a little different than the FM. Again, using multiple guides to create my own that worked. Whether or not you need to do everything I did is another story.</p>
<p>IMPORTANT: If you get an SSL cipher error after applying the certificates, you may have a setting enabled that is causing it. I discovered that having Secure Crytography Enchanced enabled can cause communication issues. To make sure it's disabled, you can find it here on the GUI</p>
<pre><code>
  Inventory > Nodes > Select Node > Settings > Global Settings > Secure Crytography Enhanced
  
</code></pre>
<p>Note: Messing with this setting will reboot the device.</p>

<h3>Pre-Requisites</h3>
<h4>Root/Intermediate Certs</h4>
<p>On the FM GUI, you will need to add the Root and CA certs to the device. In theory, adding the device on the FM will push it to the nodes. Make sure to add all Root and CA certs in your nodes chain.</p>
<pre><code>
  Settings Cog > System > Certificates > CA List > Add
  
</code></pre>
<p>The next part is what I'm not sure is needed or not, but I did it anyway. On the FM, add the Root and Intermediate certs to the Security CA list on the FM GUI</p>
<pre><code>
  Inventory > Resources > Security > CA List > Add
  
</code></pre>
<p>Add the custom certificate and key pairs for each node to the Custom SSL Certificate tab</p>
<pre><code>
  Inventory > Resources > Security > Custom SSL Certificate > Add
  
</code></pre>
<p>Add the Root and Intermediate certs to the Trust Store</p>
<pre><code>
  Settings Cog > System > Certificates > Trust Store > Add
  
</code></pre>
<p>Once you've added all of these, you can verify that the FM has pushed the Root and Intermediate CA to each device by logging into the CLI of your node and running the 
following commands</p>
<pre><code>
  enable
  config t
  show crypto certificate ca-list
  
</code></pre>
<p>This should give you a list of the root/intermediate certs on that device.</p>

<h3>Crypto Commands</h3>
<p>Once you have the pre-requisites done, you can move on to running the commands to install the new certs and keys</p>
<p>With a text editor like Notepad++, open your certificate and key files to view the content. Keep in mind that you will be copy/pasting these into the command line.
  Login to the node you're going to replace the TLS certificate on and install the new certficiate with the following commands</p>
<pre><code>
  # If you started a new session, run these commands
      enable
      config t
  # Create the public-cert pem
      crypto certificate name pudgy_cert public-cert pem "(enter after quote)
      (copy/paste certificate details, including begin/end section)
      (enter)
      " (last thing will be a finishing quote, then press enter)
  # Link private key to the pem certificate you just created
      crypto certificate name pudgy_cert private-key pem "(enter after quote)
      (copy/paste key details, including begin/end section)
      (enter)
      " (last thing will be a finishing quote, then press enter
  
</code></pre>
<p>Once you have created the certificate and linked the key to it, you can make it the default certificate for the node to use</p>
<pre><code>
  crypto certificate default-cert name pudgy_cert
  web https certificate name pudgy_cert
  
</code></pre>

<h3>Verify</h3>
<p>Go to your browser now and navigate to the nodes address. While you can't manage it from the browser, you can still reach it and see if the cert 
has been applied correctly. You can also verify that the new cert is being used by default with the following command</p>
<pre><code>
  show crypto certificate default-cert public-pem
</code></pre>


<h2>Finished</h2>
<p>That should be all there is to it. Hopefully it helps.</p>



