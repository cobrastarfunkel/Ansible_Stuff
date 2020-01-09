### LDAP Setup
##### Tested on CentOS 7
#### Before you Run the Playbook
* Configure the LDAP server in the hosts file.  
* Set the ldap port in group_vars/ldap_server if you want it to be something else.
* An encrypted vault password needs to be set to add some of the ldap stuff.  Replace the vault value in
  group_vars/ldap_server or put it in there in plaintext if you don't care.
* Create users in roles/ldap_server/vars/main.yml in the same way the example ldapusers are set.
* Turns out OpenLDAP is picky about ldif files and white space hence the overly complicated file to add users.  This will compare the output of getent passwd to the list of users you want to add.  If the user already exists it will be skipped to stop LDAP errors.  If you can't use getent for some reason to pull a user list switch the command at the top of roles/ldap_server/main.yml to pull your user list.
* Groups are managed in the ldap_server/vars/main.yml file.  It will handle the same gid by ignoring it but group names aren't checked at the moment.

Run:

    slappasswd -h {SSHA} -s your password
  Save the output of this command and put it in the /templates/db.ldif.j2 file at the bottom where it says olcRootPw.

Run:

    ansible-playbook ldap.yml --ask-vault-pass
  *Note: You don't need the --ask-vault-pass if you chose to not encrypt your password*

To test Run:

    getent passwd # For users
    getent group # For Groups
