Mitigating Bots using a DOS profile
===================================

Objective:
----------

-  Scan the web site using a spider (OWASP ZAP)
-  Create a Denial of Service Profile
-  Enable the Proactive Bot Defense
-  Associate the DoS profile with an existing virtual server
-  Estimated time for completion: **15 minutes**

Initiate a bot attack
---------------------

You are now going to use the OWASP ZAP tool to run a spider bot attack
against the Hackazon website.

1. From the **Applications Menu** in the upper left-hand corner of the
   jumpbox go to **OWASP > Proxies > ZAP**.

2. Once ZAP is open, click on **Start** when asked **Do you want to
   persist the ZAP session**.

3. In the **Quick Start** tab, in the **URL Attack** box, enter
   http://hackazon.f5demo.com and hit the **Attack** button\ **.**

.. image:: /_static/image35.png

1. Once the attack has started a **Spider** tab should appear in the
   bottom ZAP window. You will see ZAP crawling the web site. Stop the
   attack after you have observed this.

.. image:: /_static/image36.png

Creating a DoS Profile
----------------------

Now you will create a DoS profile to prevent malicious bots from
attacking the Hackazon web site.

1. Create a DoS profile using the information in the following table.
   These will create a base profile you can then modify.

+-----------------------------------------------------------------------+--------------------+-----------------------------+
| **Configuration utility**                                             |                                                  |
+=======================================================================+====================+=============================+
| **Security > DoS Protection : DoS Profiles,** then click **Create**   |                                                  |
+-----------------------------------------------------------------------+--------------------+-----------------------------+
| Configuration section                                                 |                                                  |
+-----------------------------------------------------------------------+--------------------+-----------------------------+
|                                                                       | Profile Name       | **Agility_DoS_Profile**     |
+-----------------------------------------------------------------------+--------------------+-----------------------------+
|                                                                       | Partition / Path   | **Common**                  |
+-----------------------------------------------------------------------+--------------------+-----------------------------+
|                                                                       |                    |                             |
+-----------------------------------------------------------------------+--------------------+-----------------------------+
| When finished, click                                                  | **Finished**       |                             |
+-----------------------------------------------------------------------+--------------------+-----------------------------+

1. Modify the properties of the DoS Profile by opening the new DoS
   Profile and customize the profile using the information in the table
   below.

+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| **Configuration utility**                                                    |                                                                                       |
+==============================================================================+===========================================+===========================================+
| **Security > DoS Protection > DoS Profiles: Agility_DoS_Profile**            |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| Application Security > General Settings section (select Edit to Begin)       |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Application Security                      | **Enabled**                               |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | IP Address Whitelist                      | **Not Configured**                        |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Geolocations                              | **Not Configured**                        |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Trigger iRule                             | **Disabled**                              |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| Application Security » Proactive Bot Defense section                        |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Operation Mode                            | **Always**                                |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Block requests from suspicious browsers   | **Block Suspicious Browsers - checked**   |
|                                                                              |                                           |                                           |
|                                                                              |                                           | **CAPTCHA Challenge - UNCHECKED**         |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Grace Period                              | **5 seconds**                             |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Cross-Domain Requests                     | **Allow all requests**                    |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | URL Whitelist                             | **Not Configured**                        |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| Application Security » Bot Signatures                                       |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Bot Signature Check                       | **Enabled**                               |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Bot Signature Categories                  | **Malicious Categories: Block**           |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
|                                                                              | Bot Signature Categories                  | **Benign Categories: Leave at Custom**    |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| **TPS-based Detection: Off**                                                 |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| **Stress-based Detection: Off**                                              |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| **Heavy URL Protection: Off**                                                |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| **Record Traffic: Off**                                                      |                                                                                       |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+
| When finished, click                                                         | **Update**                                |                                           |
+------------------------------------------------------------------------------+-------------------------------------------+-------------------------------------------+

.. image:: /_static/image37.png

1. Assign the DoS Profile to a virtual server. 

.. NOTE:: 

   An Application Security Policy is NOT required to apply a L7 DDoS profile.

+--------------------------------------------------------------------------------+--------------------------+-----------------------------+
| **Configuration utility**                                                      |                                                        |
+================================================================================+==========================+=============================+
| **Local Traffic > Virtual Servers > Virtual Server list: vs_auction_http**     |                                                        |
+--------------------------------------------------------------------------------+--------------------------+-----------------------------+
| Security > Policies tab                                                        |                                                        |
+--------------------------------------------------------------------------------+--------------------------+-----------------------------+
|                                                                                | Dos Protection profile   | **Agility_DoS_Profile**     |
+--------------------------------------------------------------------------------+--------------------------+-----------------------------+
| When finished, click                                                           | **Update**               |                             |
+--------------------------------------------------------------------------------+--------------------------+-----------------------------+

.. image:: /_static/image38.png

1. Using the OWASP ZAP tool, attack the website again. Notice this time
   the attack was stopped.

.. image:: /_static/image39.png

1. On you BIG-IP open a new window and go to **Security > Reporting >
   DoS > Application > Transaction Outcomes.** With a few minutes your
   attack should show up.

.. image:: /_static/image40.png

TPS-based Detection
===================

Mitigating DoS attacks usingTPS-based Detection 
------------------------------------------------

Objective:
----------

-  Observe how a Denial of Service attack is blocked using TPS-based
   Detection
-  Estimated time for completion: **15 minutes**

1. Edit the DoS Profile, set the Operation Mode of Proactive Bot Defense
   to Off, Bot Signatures to Off, and TPS based Detection to Blocking.
   These are deliberately low.

   Note: in this lab exercise, we will disable the Proactive Bot Defense
   and Bot Signatures in order to use our tools to show the TPS
   Detection DOS blocking mechanism in a classroom environment. In a
   real-life scenario, these would be typically enabled (in addition to
   the TPS based or other detection mechanisms) to increase the
   effectiveness of the overall DoS protection.

+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| **Configuration utility**                                                              |                                                             |
+========================================================================================+=======================+=====================================+
| **Security > DoS Protection > DoS Profiles: Agility_DoS\_Profile**                     |                                                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| Application Security > Proactive Bot Defense section                                   |                                                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | Operation Mode        | **Off**                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| Application Security > Bot Signatures                                                  |                                                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | Bot Signature Check   | **Off**                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| Application Security > TPS-based Detection                                             |                                                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | Operation Mode        | Blocking                            |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | By Source IP          | TPS increased by 100%               |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | By Source IP          | Reached at least 15 TPS             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | TPS reached           | 20 TPS                              |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | Request Blocking      | Checked, Block All                  |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | By Geolocation        | No mitigation                       |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | By URL                | No mitigation                       |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | Site Wide             | No mitigation                       |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | Prevention Duration   | Escalation Period 120 seconds       |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
|                                                                                        | Prevention Duration   | De-escalation Period 7200 seconds   |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| **Stress-based Detection: Off (Uncheck Request Blocking checkbox if it is checked)**   |                                                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| **Heavy URL Protection: Off (Uncheck Request Blocking checkbox if it is checked)**     |                                                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| **Record Traffic: Off (Uncheck Request Blocking checkbox if it is checked)**           |                                                             |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+
| When finished, click                                                                   | **Update**            |                                     |
+----------------------------------------------------------------------------------------+-----------------------+-------------------------------------+

.. image:: /_static/image41.png

1. Open a new browser tab/window and go to **Security >> Reporting >>
   DoS >> Overview** and open another tab/window and go to **Security >>
   Reporting >> DoS >> Transaction Outcomes**.

2. With the Overview window to the front use Apache Bench to attempt to
   DoS the Hackazon website. Open a new terminal window and type::

   ab -n 2500 -c 100 -k -r http://hackazon.f5demo.com/

3. You should see an attack notification in the Overview window.

.. image:: /_static/image42.png

4. You can also observe the attack via the pool statistics. Notice two
   things: in the Pools Statistics (monitor the Current Connections
   counter) show a large number of connections and traffic throughput
   for a few seconds until the Attack is being detected (Attack is shown
   from the second tab showing the Security/Reporting/DOS/Overview). As
   soon as you see the attack (actually slightly prior to that as it
   takes about a second to be reported), from the Pools Statistics you
   will notice traffic tapering off and Connections going to 0 – proof
   that ASM has stopped (blocked) the attack

5. As a final step, verify that the DoS Blocking took place, refresh the
   Transaction Outcomes by hitting the **Go** button.

6. As a final step, verify that the DoS Blocking took place, refresh the Transaction Outcomes by hitting the Go button.
7. Hover your mouse over the most recent spike in TPS and notice that it shows that DoS Blocking took place:

.. image:: /_static/image42.png
