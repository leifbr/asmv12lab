Getting Started
---------------

Please follow the instructions provided by the instructor to start your
lab and access your jump host.

.. NOTE::
	 All work for this lab will be performed exclusively from a Xubuntu
	 jumphost. No installation or interaction with your local system is
	 required.

Lab Topology
~~~~~~~~~~~~

Each student will have a BIG-IP VE environment with IP addressing as
below:

.. image:: /_static/image2.jpg

- 2 x F5 BIG-IP VE (v12.1)
- 1 x Linux LAMP Webserver (xubuntu 14.04)
- 1 x Xubuntu Jumphost (xbunutu 17.04)

Lab Components
^^^^^^^^^^^^^^


.. list-table::
    :widths: 20 40 40
    :header-rows: 1
    :stub-columns: 1

    * - **Component**
      - **VLAN/IP Address(es)**
      - **Credentials**
    * - xubuntu jumpbox
      - - **Management:** 10.1.1.51
        - **client_vlan:** 10.1.10.51
        - **server_vlan:** n/a
      - ``f5student``/``f5DEMOs4u``
    * - bigip01.f5demo.com
      - - **Management:** 10.1.1.245
        - **client_vlan:** 10.1.10.245
        - **server_vlan:** 10.1.20.245
      - ``admin``/``admin``
    * - LAMP server
      - - **Management:** 10.1.1.252
        - **client_vlan:** n/a
        - **server_vlan:** 10.1.20.252
      - ``root``/``default``

Accessing the Ravello lab environment
-------------------------------------

a. Open a browser and go to http://training.f5agililty.com/<lab
       number>/X (where **X** is your student number)

b. Look for the **xubuntu-jumpbox-vxx**. You will use the xubuntu
       jumpbox for all the labs. (see below)

.. image:: /_static/image3.png

a. You can click on **RDP** to RDP to the Xubuntu jumpbox or you can
   select the **CONSOLE** link and access the jumpbox via your browser.
   **The CONSOLE link requires you turn off pop-up blockers.**

.. image:: /_static/image4.png




