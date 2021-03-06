# Overview

This interface layer handles the communication between the Ceph Monitor
and a client that requires an admin key.

# Usage

## Requires

This interface layer will set the following states, as appropriate:

  * `{relation_name}.connected` The ceph client has been related to a provider.
  * `{relation_name}.pools.available` The pools requested have been created,
  The following accessors will be available:
   - key - The mds cephx key
   - admin_key - The cephx admin key
   - auth - Whether or not strict auth is supported
   - mon_hosts - The public addresses list of the monitor cluster


Client example:

```python
@when('ceph-mds.connected')
def storage_ceph_connected(ceph):
    ceph.announce_mds_name()
    ceph.initialize_mds(hookenv.service_name())

@when('ceph-mds.pools.available')
def ceph_connected(ceph_info):
  charm_ceph_conf = os.path.join(os.sep, 'etc', 'ceph', 'ceph.conf')
  cephx_key = os.path.join(os.sep, 'etc', 'ceph', 'ceph.client.admin.keyring')

  ceph_context = {
      'auth_supported': ceph_client.auth,
      'mon_hosts': ceph_client.mon_hosts,
  }

  with open(charm_ceph_conf, 'w') as cephconf:
    cephconf.write(render_template('ceph.conf', ceph_context))

  # Write out the cephx_key also
  with open(cephx_key, 'w') as cephconf:
    cephconf.write(ceph_client.key)
```
