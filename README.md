<h1>How to use puppet to automatically manage your servers configuration</h1>
<i>By Jeremie Kamuina</i><br>
<addr>
  <a href="mailto:jrmkams@gmail.com">jrmkams@gmail.com</a>
  <br>
  <a href="www.linkedin.com/in/jeremie-kamuina-129b06186">LinkedIn : www.linkedin.com/in/jeremie-kamuina-129b06186</a>
</addr>
<br><br>
<div>
  <ol> <li><p><h4><b>context:</b></h4></li> You have a server in which you want to install Apache, flask, git, clône your project from github and create a configuration file which will regulate the operation of your instance. It's simple using a few Linux commands you can install and start your applications and set up your configuration. But imagine you have 1000 servers, how do you install all these applications and make sure they are still running? this is why in this tutorial we introduce you to the Puppet tool which is a configuration manager and allows you to automate tasks in the configuration of your servers</p>
  <br>
  <img src="Puppet_transparent_logo.svg" alt="logo puppet">
  <br>
  <li>
  <h4>
    <b>How does puppet work ?</b>
    </h4>
    <p>Puppet is based on a master-slave communication system. There is therefore a Master server and one or more slave servers linked to it. The master contains a module which in turn contains a manifest, a template and static files which can be downloaded by the slave servers  and the slaves servers contain each an agent and a factor.</p>
    <p>The Slave servers sends a SSL certificate t the master to ask for a secure connection, the master responds by sinning the certificate, once the slave server (client node) is aware that the master has signed the certificate his factor analyses it's current state (configurations and installed software informations) and these informations will be sent the master as facts and any of thoses facts will be compared to the desired state specified in Puppet manifests on the Puppet Master server. If there is a difference between them the missing elements will be installed, created or started</p>
  
  </li>
  <li><b>Prerequisites</b></li>
  <ul type="square">
    <li>3 Amazon Linux 2 EC2 instances (CentOS 7)</li>
    <li>basic knowledge of linux</li>
    <li>basic knowledge of Ruby</li>
    <li>basic knowledge of git</li>
  </ul>
  <li><b>Steps</b></li>
  <h5><b>In the Master Server</b></h5>
  <ul type="square">
    <li>Update the server</li>
    <pre><code>sudo yum update</code></pre>
    <img src="update result.png" alt="the result of update">
    <br>
    <li>Rename the master hostname as puppet and review the new hostname</li>
    <br>
    <pre><code>sudo hostnamectl set-hostname puppet</code></pre>
    <pre><code>hostname</code></pre>
    <p>
      <img src="hostname result.png" alt="hostname change result">
    </p>
    <li>Stop any running firewall if neccessary and review the firewall status</li>
    <pre><code>sudo systemctl stop firewalld</code></pre>
    <pre><code>sudo systemctl status firewalld</code></pre>
    <br>
    <img src="firewalld status.png" alt="firewalld status">
    <br>
    <li>Download Puppet labs</li>
    <br>
    <pre><code>sudo rpm -Uvh https://yum.puppet.com/puppet6-release-el-7.noarch.rpm</code></pre>
    <br>
    <img src="dowload puppetlabs result.png" alt="dowload puppetlabs result.png>
  </ul>
  </ol>
</div>
