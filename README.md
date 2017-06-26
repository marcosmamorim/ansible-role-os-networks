OpenStack Networks
==================

This role can be used to register networks, subnets and routers in neutron
using the os\_network, os\_subnet and os\_router modules.

Requirements
------------

The OpenStack neutron API should be accessible from the target host.

Role Variables
--------------

`os_networks_venv` is a path to a directory in which to create a virtualenv.

`os_networks_auth_type` is an authentication type compatible with the
`auth_type` argument of `os_*` Ansible modules.

`os_networks_auth` is a dict containing authentication information
compatible with the `auth` argument of `os_*` Ansible modules.

`os_networks` is a list of networks to register. Each item should be a
dict containing the following items:

- `name`: Name of the neutron network.
- `provider_network_type`: Provider type of the neutron network.
- `provider_physical_network`: Provider physical network of the neutron
  network.
- `provider_segmentation_id`: Provider segmentation ID of the neutron network.
- `shared`: Whether the neutron network is shared.
- `external`: Whether the neutron network is external.
- `project`: Optionally create this network for a project other than admin.
- `subnets`: A list of subnets to create in this network. Each item should
   be a dict containing the following items:
   - `name`: Name of the neutron subnet.
   - `cidr`: CIDR representation of the neutron subnet's IP network.
   - `gateway_ip`: IP address of the neutron subnet's gateway.
   - `allocation_pool_start`: Start of the neutron subnet's IP allocation
     pool.
   - `allocation_pool_end`: End of the neutron subnet's IP allocation pool.

`os_networks_routers` is a list of routers to create. Each item should be a
dict containing the following items:

- `name`: Name of the neutron router.
- `interfaces`: List of names of subnets to attach to the router
  internal interface.
- `network`: Unique name or ID of the external gateway network.
- `project`: Optionally create this router for a project other than admin.

Dependencies
------------

This role depends on the `stackhpc.os-shade` role.

Example Playbook
----------------

The following playbook registers a neutron network, subnet and router.

    ---
    - name: Ensure networks, subnets and routers are registered
      hosts: neutron-api
      roles:
        - role: os-networks
          os_networks_venv: "~/os-networks-venv"
          os_networks_auth_type: "password"
          os_networks_auth:
            project_name: <keystone project>
            username: <keystone user>
            password: <keystone password>
            auth_url: <keystone auth URL>
          os_networks:
            - name: net1
              provider_network_type: vlan
              provider_physical_network: physnet1
              provider_segmentation_id: 1234
              shared: true
              external: false
              subnets:
                - name: subnet1
                  cidr: 10.0.0.0/24
                  gateway_ip: 10.0.0.1
                  allocation_pool_start: 10.0.0.2
                  allocation_pool_end: 10.0.0.254
          os_networks_routers:
            - name: router1
              interfaces:
                - subnet1
              network: net1

Author Information
------------------

- Mark Goddard (<mark@stackhpc.com>)