Initial configuration of the |bip|
==================================

Open the Chrome browser and log into the BIG-IP GUI to verify the BIG-IP is up.

Go to **https://10.1.1.245**::
 
   User: admin  
   Password: admin

Now you will perform an initial configuration via command line. Open a terminal window from the taskbar at the bottom of the jumpbox.

Log in to the BIG-IP using the command:: 

     ssh root@10.1.1.245   
     The password is **default.**

At the BIG-IP prompt, enter **tmsh**. This will place you in the BIG-IP command line mode. Copy and paste the following TMSH commands into the shell.

.. code-block:: bash
 
    #
    # Lab Prep for Application Security Manager (ASM) 101- bigip245
    #
    
    # BEGIN COPY - Lab prep
    # Create VLANs
    create net vlan client_vlan interfaces add { 1.1 { untagged } }
    create net vlan server_vlan interfaces add { 1.2 { untagged } }
    
    # Create Self IPs
    create net self client_ip address 10.1.10.245/24 vlan client_vlan
    create net self server_ip address 10.1.20.245/24 vlan server_vlan
    
    # Create a default route
    create net route def_gw network 0.0.0.0/0 gw 10.1.10.1
    
    # Build Pools
    create ltm pool www_pool members add { 10.1.20.11:80 10.1.20.12:80 10.1.20.13:80 } monitor http
    create ltm pool hackazon_pool members add { 10.1.20.20:80 } monitor http
    
    # Build Virtual Servers
    
    create ltm virtual vs_hackazon_http destination 10.1.10.20:80 pool hackazon_pool ip-protocol tcp source-address-translation { type automap } profiles add { http }
    create ltm virtual vs_hackazon_https destination 10.1.10.20:443 pool hackazon_pool ip-protocol tcp source-address-translation { type automap } profiles add { http clientssl }
    
    # Provision ASM
    modify sys provision asm level nominal
    
    # Archive the initial ASM build
    save sys ucs asm_101_basic_build
    # END COPY - Lab prep

Paste the commands into the terminal window at the **tmsh** prompt.

.. NOTE::

   You can also open the **Lab Guides** link on the bookmarks bar in a new tab/window.  In Open the **ASM 101.txt** file and copy the commands from there.

The BIG-IP will take several minutes to come back online.
   - Good time for a bathroom break. Smoke 'em if you got 'em.
  
Verify the virtual server and web site are up and running.
   - Go to **Local Traffic > Network Map**. There should be two virtual servers and all should be available (green).
   - Open up the Firefox browser. Go to http://hackazon.f5demo.com and
      https://hackazon.f5demo.com

Create an ASM Policy
====================

Objective:
----------

-  Create a security policy using automatic policy building
-  Enable application security logging profile
-  Estimated time for completion: **20 minutes**

This lab will demonstrate how to create and build a security policy
using automatic policy building.

..

Before you begin, the penetration testing tool, OWASP ZAP, has already been run against the website and the results have been saved. Let’s take a look at the test to see the results. You will use this information to plug a few holes.

- From the Applications Menu in the upper left-hand corner of the jumpbox go to **OWASP > Proxies     ZAP**.
- Once ZAP is open, click on **Start** when asked "Do you want to persist the ZAP session?".
- Under **File** select **Open Session** and select **hackazon-zap-session.session.**
  
  - Once the session is loaded, go to the **Alerts** tab, you will see a number of vulnerabilities found.

.. image:: /_static/image5.png

We will be focusing on the CrossSite Scripting (XSS) and SQL Injection vulnerabilities discovered specifically, on the Search page and User Login page.

- If you expand the tabs you can see the vulnerabilities found. Click on the page to get more detail.

Creating Application Security Policy
====================================

On the Main tab, click **Security** > **Application Security** > **Security Policies**. 
The Active Policies screen opens.

Click the **Create** button. The Deployment wizard opens to the **Select Local Traffic Deployment Scenario** screen.

- Select the **Existing Virtual Server** button and hit **Next**

.. image:: /_static/image6.png

- Select **HTTP** for the protocol and **vs\_hackazon\_http** as the HTTP Virtual Server and hit **Next**.

.. image:: /_static/image7.png

   - Select **Create a security policy automatically (recommended)** and hit **Next…**

.. image:: /_static/image8.png

- Name the security policy **hackazon_asm** and select **Unicode (utf-8)** for the application language and *de-select* all other options; select **NEXT**

.. image:: /_static/image9.png

- In the Configure Attack Signatures section, move **Unix/Linux**, **Apache,** **PHP**, and **MySQL** from the Available Systems column to the Assigned Systems column. Notice that there are pre-defined attack signatures associated with this security policy, and select **NEXT**

.. image:: /_static/image10.png

- Select **Comprehensive** policy type and change the learning speed to **Fast**. In the Trusted IP Addresses section, add the **10.1.10.0** network address (with netmask 255.255.255.0), click **Add**. Leave the remaining fields with their default settings and select **NEXT**

.. image:: /_static/image11.png

- Review the Security Policy Configuration Summary and click **Finish**.
   - There may be a 2 to 3 minute delay while the policy is initializing. 

.. image:: /_static/image12.png

- At the **Security Policy Properties** page, select **Save** and then the **Apply Policy** button.

.. image:: /_static/image13.png

Enable Application Security Logging
===================================

In the Configuration Utility, open the **Security > Event Logs: Logging Profiles** then click **Create.** 

- Enter a Profile Name **asm\_allrequests**, select the checkbox for **Application Security,** change the **Configuration** dropdown to **Advanced**, and then set the **Response Logging** dropdown to **For All Requests.** Change the **Request Type** under storage filter to **All Requests.** Click **Finished.**

.. image:: /_static/image14.png

In the Configuration Utility, open the **Local Traffic > Virtual Servers.** Select **vs\_hackazon\_http** and click the **Security > Policies.** 

- Change the **Log Profile** option to **Enabled** and then move the **asm\_allrequests** from Available to Selected and click **Update**.
   - Note that the Application Security Policy has already been applied per the wizard.

.. image:: /_static/image15.png

Generate trusted learning suggestions by browsing the Auction site via the protected virtual server.

- Use the **Firefox** browser to access **http://hackazon.f5demo.com**

- Select **Sign In** in the upper right corner and attempt to login to the site using guessed credentials of **student/student** and submit them with the **Sign In** button.

.. image:: /_static/image16.png

In the Configuration Utility, open the **Security > Application Security > Policy > Audit > Log** page and you’ll notice that as a result of interaction with the web site, Elements are being added to the Policy by the Policy Builder as the ASM is learns the application. 

- Click the **Apply Policy** button if **Changes have not been applied yet** is displayed.

.. image:: /_static/image17.png

- Return to the Hackazon site, select **Sign In** and re-attempt a login to the site using **student1234567890/student1234567890**.

- Return to the **Security > Application Security > Policy > Audit > Log** page in the F5 Configuration Utility and notice that further updates have been made to the Policy. Click the**Apply Policy** button if **Changes have not been applied yet** is displayed

.. image:: /_static/image18.png

- Return to the Hackazon site one more time, select **Sign In** and re-attempt a login to the site using an email address as the username (i.e. student@example.com) and any password.

- Return to the **Policy Audit Log** (step c above) and notice that updates were made to the **username** Parameter element to allow the **@** sign. Click the **Apply Policy** button if **Changes have not been applied yet** is displayed

The login form has now been "learned" by the Policy Builder and added to the policy.

In the Configuration Utility, open the **Security > Application Security > Policy Building > Enforcement Readiness** page. You will see that several elements were discovered. Click through each of the numbers to see which elements were found.

.. image:: /_static/image19.png

- Open **Security > Event Logs > Application > Requests.** Under **Requests Lists** select **All Requests** from the drop down. You should see log entries with recent timestamps.

.. image:: /_static/image20.png

In the Configuration Utility, open the **Security > Application Security > Policy Building > Learning and Blocking Settings**. 

- Make sure the **Advanced** view option is selected.

.. image:: /_static/image21.png

You are now finished building the policy for this exercise and youneed to remove the client network from the trusted IP addresses so that you can attempt to attack the Auction Website from your client PC. 
- From the Policy Building Process section expand the **Trusted IP Addresses** section and remove the 10.1.10.0/24 entry from the Trusted IP Addresses, and click **Save.**

.. image:: /_static/image22.png

At the top of the page, click the **Apply Policy** button to apply your changes.

.. image:: /_static/image23.png

.. WARNING::

   Make sure your **Trusted IP Addresses** are empty or it will impact the next lab.

