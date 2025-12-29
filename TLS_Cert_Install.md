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

<h4>Fin</h4>
<p>That's all that should need to be done for the FM. I will post the guide for the nodes below.</p>


<h2>Gigamon Nodes</h2>
<p>In Progress</p>
