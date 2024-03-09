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
  <h5><b>A. In the Master Server</b></h5>
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
    <img src="dowload puppetlabs result.png" alt="dowload puppetlabs result.png">
    <br>
    <li>Install puppetserver</li>
    <br>
    <pre><code>sudo yum install puppetserver -y</code></pre>
    <br>
    <img src="puppetserver install result.png" alt="puppetserver install result">
    <br>
    <li>Configure puppetserver</li>
    <i>Open the configuration file with nano and change the memory size from 2g to 512m on "JAVA-AGS"</i>
    <br>
    <br>
    <pre><code>sudo nano /etc/sysconfig/puppetserver</code></pre>
    <br>
    <img src="configuration file.png" alt="configuration file">
    <br>
    <i>Press Ctr + O and Enter and Ctr + x to save and quit</i>
    <br><br>
    <li>Start and enable puppetserver</li>
    <br>
    <pre><code>sudo systemctl start puppetserver</code></pre>
    <pre><code>sudo systemctl enable puppetserver</code></pre>
    <br>
    <img src="start puppetserver.png" alt="start puppetserver">
  </ul>
  <h5><b>B. In the Slave Server (Client Node)</b></h5>
  <i>Note : do the following steps in each of your client nodes (slave servers)</i>
  <br><br>
  <ul type="square">
    <li>Add the master private ip address in the slave host configuration file</li>
    <i>Open the configuration file with nano and add the following line at the end of the file<br>
      your_master_private_ip_address puppet puppet-master
    </i>
    <pre><code>sudo nano /etc/hosts</code></pre>
    <br>
    <img src="pngchange host.png" alt="change host">
    <br>
    <i>Tape Ctr + O, Enter, Ctr + x to save and quit</i>
    <br>
    <li>Download Puppet labs</li>
    <br>
    <pre><code>sudo rpm -Uvh https://yum.puppet.com/puppet6-release-el-7.noarch.rpm</code></pre>
    <br>
    <img src="dowload puppetlabs result.png" alt="dowload puppetlabs result.png">
    <br>
    <li>Install puppet agent</li>
    <br>
    <pre><code>sudo yum install -y puppet-agent</code></pre>
    <br>
    <img src="install agent.png" alt="install agent">
    <li>Start puppet-agent</li>
    <br>
     <pre><code>sudo /opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true</code></pre>
    <br>
    <img src="start puppetagent.png" alt="start puppetagent">
    <br>
    <h5><b>C. In the Master Server</b></h5>
    <li>Display the list of received certificates</li>
    <pre><code>sudo /opt/puppetlabs/server/bin/puppetserver ca list --all</code></pre>
    <br>
    <img src="list of certificates.png" alt="list of certificates">
    <br>
  <i>Note : Our master received two certificates because we have worked with two client nodes (slave instances)</i>
  <br>
  <br>
  <li>Sign certificates</li>
  <ul>
    <li>Sign the first client node certificate</li>
    <pre><code>sudo /opt/puppetlabs/bin/puppetserver ca sign --certname your_first_client_node_hostname</code></pre>
    <br>
    <img src="sign1.png" alt="signing the first">
<p>
    <li>Sign the second client node certificate</li>
    <pre><code>sudo /opt/puppetlabs/bin/puppetserver ca sign --certname your_second_client_node_hostname</code></pre>
    <br>
    <img src="sign2.png" alt="signing the second"></p>
  </ul>
  <li>Create a manifest file and check if the file was created</li>
    <br>
    <pre><code>sudo touch /etc/puppetlabs/code/environments/production/manifests/sample.pp</code></pre>
    <pre><code>sudo ls /etc/puppetlabs/code/environments/production/manifests/sample.pp</code></pre>
    <br>
    <img src="manifest created.png"  alt="manifest created">
  </ul>
  
  </ol>
</div>
