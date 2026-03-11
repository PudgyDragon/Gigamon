<h1>Gigamon FM IP Change</h1>
<p>Guide for updating the IP address on your Gigamon FM. Nothing crazy. Legitimate guides for what I'm doing can be found here</p>
<a href="https://docs.gigamon.com/doclib611/Content/GV-FM-Install/Initial_GigaVUE-FM_Configuration__2.html">Initial GigaVUE‑FM Configuration</a>

<a href="https://docs.gigamon.com/doclib611/Content/GV-FM-UG/Add_New_Physical_Node_or_Cluster_to_GigaVUE_FM.html">Add New Physical Node or Cluster to GigaVUE‑FM</a>

<h2>Pre-Requisite</h2>
<p>Before changing the IP address, you may want to unmanage any nodes the FM is currently managing. This should help prevent 
the possibility of any relationship issues. It can be done from the FM GUI</p>
<pre><code>
  Inventory > Physical > Nodes > Select the Node > Actions > Delete
  
</code></pre>
<p>Delete sounds scary, but this is the way that it is done.</p>

<h2>IP Change</h2>
<p>Once your nodes are no longer managed by the FM, proceed with changing the IP. This will be done through the CLI with the following command</p>
<pre><code>
  fmctl set ip static ipv4/subnet gateway
  
</code></pre>
<p>The IPv4 will be your new IP, the subnet will be the CIDR notation for your subnet mask, and the gateway will be whatever your gateway ip is. An example</p>
<pre><code>
  fmctl set ip static 10.10.10.14/22 10.10.10.1
  
</code></pre>
<p>Once complete, you will need to reboot the device.</p>

<h2>Manage Nodes</h2>
<p>Once your device comes back up, manage any nodes that you removed previously through the FM GUI</p>
<pre><code>
  Inventory > Physical > Nodes > Add
  
</code></pre>
