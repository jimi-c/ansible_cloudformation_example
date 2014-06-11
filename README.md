How to Use
==========

This playbook assumes the following:

* You have set the AWS environment variables required for boto.
* You have the EC2 dynamic inventory script and .ini file in /etc/ansible
* The private/public key you want to use for this are in ~/.ssh/ and are named id_rsa.ansible and id_rsa.ansible.pub. To change this, just edit the `build_datacenter.yml` playbook.

To spin up the environment:

    ansible-playbook -v --private-key=~/.ssh/id_rsa.ansible -i /etc/ansible/ec2.py build_datacenter.yml --ask-vault-pass

If for some reason the drupal installation fails, or if you'd like to force it to wipe/reinstall, you can add `-e force_drupal_install=yes`.

The first time the playbook runs, it will create the cloudformation deployment. Since the EC2 inventory does not yet know about the hosts it is creating, once the environment is done building it will simply skip the next few tasks. To actually configure the servers and deploy the software, simply re-run the above command.

Once you're done with the environment, simply destroy it with this command:

    ansible-playbook -v destroy_datacenter.yml

Variables
=========

All of the following variables can be set to modify the environment, and are defined in the file env_variables. You can change them there, or override them as needed with the `--extra-vars/-e` command option for `ansible-playbook`.

* `demo_stack_name` - the name of the cloudformation stack. This will also be used for the key name. Defaults to `ansible-demo`.
* `demo_region` - the region in which to build the stack. Defaults to `us-east-1`.
* `web_instance_type` - the instance type to use for the web servers. Defaults to `m1.small`.
* `web_server_port` - The port nginx will listen on by default. Defaults to `8080`.
* `web_num_instances` - The number of instances to add to the auto-scaling group. Currently, the same number is used for both the min and max. Defaults to `5`.
* `db_name` - the name of the database server. Defaults to `ansibledemo`.
* `db_user` - the name of the MySQL admin user. Defaults to `admin`.
* `db_allocated_storage` - this value is the size of the database storage allocated. Defaults to `5`.
* `db_instance_class` - this value is the instance type to use for the RDS database. Defaults to `db.m1.small`.
* `db_multi_az` - if set to `true`, the RDS database will span multiple AZs. Defaults to `false`.
* `db_read_replica` - if set to `true`, the RDS database will have a read replica created. Defaults to `false`.

Vault File
==========

The `secure.yml` file is vault-encrypted. To modify it, the default password is `secret`. By default, it contains:

    ---
    db_password: testing1234
    admin_password: Password1

You can use the `ansible-vault edit secure.yml` command to modify it, or use `ansible-vault rekey secure.yml` to change the 
