# Manage a simple VLAN service

The **vlan.yml** playbook in this repository manages (add/modify/remove) a simple VLAN service described in the *services.yml* data model on NX-OS switches.

## Explore the progress

I created a number of git branches while developing this example to allow you to explore various stages of the project. Use <code>git checkout *branch*</code> to check out the desired stage of the project into your working directory.

The branches you can explore are:

* ***VLAN_Initial*** - initial implementation that provisions VLAN database, interfaces and access VLANs straight from the services data model
* ***VLAN_Data_Model*** - transforms services data model into per-node data model at the beginning of the playbook for easier processing. The introduction of per-node data model simplified the provisioning process.
* ***VLAN_Decommission*** - add support for service decommissioning and customer site description in the services data model, which triggered changes in data model transformation and minor adjustments in service provisioning process.
* ***VLAN_Validation*** - validates successful deployment of VLAN services: VLANs are created and configured as access VLANs on service ports.
* ***VLAN_Cleanup*** - cleanup of VLANs and service ports no longer used by active services.
* ***VLAN_PreDeploy_Check*** - check the actual hardware before service deployment
* ***VLAN_MultiVendor*** - add multi-platform/vendor support
* ***Database*** - integration with an external database

## Data model

Service definition file (services.yml) is a YAML dictionary with every customer being a key/value pair. Each customer record has a **vlan** key (VLAN# used for this customer) and a list of active service ports (**ports** key). Later stages of the project add **state** key to indicate a service should be removed (when **state** is set to **absent**).

The list of service ports is a list of dictionaries, each one of them having:
* **node**: device on which the service port is configured
* **port**: port on the device
* **site**: site name within customer network (used for interface descriptions)

Here's a sample service definition file:

    ---
    ACME:
      vlan: 100
      ports:
      - { node: leaf-1, port: "Ethernet2/1", site: "ACME Downtown" }
      - { node: leaf-2, port: "GigabitEthernet0/1", site: "ACME Remote" }

    Wonka:
      vlan: 200
      state: absent
      ports:
      - { node: leaf-1, port: "Ethernet2/3"}
      - { node: leaf-2, port: "GigabitEthernet0/3" }

## Tests

All the tests included in this project are in *tests* directory:

* ***model.yml*** - creates per-node data models and stores them in *printouts* directory
* ***cleanup-test.yml*** - test harness for the cleanup module

The tests use multiple scenarios stored in *inputs* subdirectory. These scenarios are created from service data model or **show** commands executed on NX-OS with the **create-*test*-scenario.sh** scripts.
