LDAP Authenticator, An utility to fetch users from LDAP server

Description
This utility makes connection to LDAP server and gets list of users based on search filter passed.

What does this this tool offer?
	Fetches list of users defined LDAP server tree
    Supports multiple security protocols - TLSv1, TLSv1.1 and TLSv1.2.
    Supports complex search filters for user search under search root.
    Able to list attributes associated with username being searched for.

Should work with any LDAP Server. Successfully tested with OpenLDAP and Active Directory.

Download 
https://github.com/kishorrekonda/LDAPAuthenticator/blob/master/FetchLDAPUsers.zip

Usage
Extract LDAPAuthenticator.zip to get LDAPAuthenticator.jar and sample input file. You would have to replace sample values for all the properties with real values received from your LDAP server before executing following command.

jdk/bin/java -jar LDAPAuthenticator.jar [location-of-properties-file]

Configure following properties with respective values as input file
LDAP_SERVER_HOST = [host]
LDAP_SERVER_PORT = [port]
LDAP_SEARCH_ROOT = [ldap_search_root]
LDAP_BIND_PRINCIPAL = [ldap_admin_user]
LDAP_BIND_PASSWORD = [ldap_admin_password]
LDAP_SEARCH_FILTER = (uid=*)
LDAP_IS_SECURE = Yes/No
LDAP_TRUST_STORE = [path-to-jks-file]
LDAP_TRUST_STORE_PASS = [password-of-truststore-jks]
LDAP_KEY_STORE = [path-to-jks-file]
LDAP_KEY_STORE_PASS = [password-of-keystore-jks]
LDAP_SSL_DEBUG = Yes/No
LDAP_TLS_VERSION = TLSv1/TLSv1.1/TLSv1.2
SEARCH_CONTROL_COUNT_LIMIT=10
SEARCH_CONTROL_TIME_LIMIT=20000

Documentation of properties 

LDAP_SERVER_HOST* 			Hostname or IP of LDAP Server.
LDAP_SERVER_PORT 			Port number of LDAP server. Use 1 if there is no port associated with LDAP server but want to go just with LDAP_SERVER_HOST.
							hint - 389 is usual LDAP port if non TLS/SSL. 636 is Usual SSL/TLS port.
LDAP_BIND_PRINCIPAL* 		Identity of the principal to authenticate, which enables the system to perform queries.
							sample: cn=Managers,dc=support,dc=com
LDAP_BIND_PASSWORD* 		Password of LDAP_BIND_PRINCIPAL on LDAP server.
LDAP_SEARCH_ROOT* 			Root from which the user query is based while searching for user being authenticated.
LDAP_SEARCH_FILTER* 		Valid search filter to look up for users on LDAP server
							sample: (uid=*)
							sample: (&(uid=*)(cn=*)
LDAP_IS_SECURE 				Specify whether it is TLS/SSL connection. Yes or No. Default is No
LDAP_TRUST_STORE 			File path to LDAP trust store (.jks file). Required if LDAP_IS_SECURE=Yes
LDAP_TRUST_STORE_PASS 		Password that allows access to LDAP_TRUST_STORE. Required if LDAP_TRUST_STORE is specified
LDAP_KEY_STORE 				File path to LDAP client key store (.jks file). Required if LDAP_IS_SECURE=Yes and client SSL/TLS authentication is enabled
LDAP_KEY_STORE_PASS 		Password that allows access to LDAP_KEY_STORE. Required if LDAP_KEY_STORE is specified
LDAP_SSL_DEBUG 				Tells if SSL traces are needed. Valid values are Yes or No
LDAP_TLS_VERSION 			Specify TLS protocol to be used. Valid values are TLSv1, TLSv1.1, TLSv1.2 or their combination such as TLSv1-TLSv1.2. Default is TLSv1.2. It is used if LDAP_IS_SECURE=Yes
SEARCH_CONTROL_COUNT_LIMIT 	Limits how many user records to be pulled for the given LDAP_SEARCH_FILTER
SEARCH_CONTROL_TIME_LIMIT 	Timeout in milli-sec

Sample Execution:
$/b2b_install/jdk/bin/java -cp ListLDAPUsers.jar ListLDAPUsers ldapuserlist.properties
#########
Reading property file..
Input configuration: {SEARCH_CONTROL_TIME_LIMIT=5000, LDAP_BIND_PASSWORD=secret, SEARCH_CONTROL_COUNT_LIMIT=5, LDAP_SEARCH_ROOT=ou=resources,dc=support,dc=com, LDAP_SERVER_HOST=ldap-host, LDAP_IS_SECURE=Yes, LDAP_SSL_DEBUG=No , LDAP_BIND_PRINCIPAL=cn=Manager,dc=support,dc=com, LDAP_TRUST_STORE=/tmp/LDAP_TrustStore.jks, LDAP_SEARCH_FILTER=(sn=*), LDAP_TRUST_STORE_PASS=password, LDAP_SERVER_PORT=636, LDAP_TLS_VERSION=TLSv1.2}
Reading property file complete.
########
Making LDAP connection by service user...
SSL/TLS enabled connection
{java.naming.provider.url=ldap://ldap-host:636, java.naming.factory.initial=com.sun.jndi.ldap.LdapCtxFactory, java.naming.security.principal=cn=Manager,dc=support,dc=com, java.naming.security.authentication=simple, java.naming.security.credentials=secret, java.naming.security.protocol=ssl, java.naming.ldap.factory.socket=LDAPTLSVersionController, com.sun.jndi.ldap.read.timeout=5000}
Found internal config file /com/sterlingcommerce/security/jcae/provider.txt, loading it now!
Done loading it.
********* This LDAP Client utility supports TLSv1,TLSv1.1,TLSv1.2 **********
Client is currently configured to use protocols: [TLSv1.2]
TLS Alias name: TLSv1.2
TLSVersionController obtained TLS configuration :  tlsAlias=TLSv1.2 tlsVersions=[TLSv1.2]
Successful connection
#########
Searching for user record on LDAP server....
Search Filter : (sn=*)
-------------------------------------
1)
User Distinguished Name: cn=aa_ldap1,ou=resources,dc=support,dc=com
User Name: cn=aa_ldap1
Attributes: {userpassword=userPassword: [B@de0b4cbc, uid=uid: aa_ldap1, objectclass=objectClass: person, top, uidObject, sn=sn: 23456, cn=cn: aa_ldap1}
-------------------------------------
2)
User Distinguished Name: cn=tester,ou=resources,dc=support,dc=com
User Name: cn=tester
Attributes: {userpassword=userPassword: [B@351b20ab, uid=uid: tester, objectclass=objectClass: person, top, uidObject, sn=sn: myserial, cn=cn: tester}
-------------------------------------
3)
User Distinguished Name: cn=tester2,ou=resources,dc=support,dc=com
User Name: cn=tester2
Attributes: {userpassword=userPassword: [B@50d25d65, uid=uid: tester2, objectclass=objectClass: person, top, uidObject, sn=sn: 123457, cn=cn: tester2}
-------------------------------------
4)
User Distinguished Name: cn=kishore,ou=resources,dc=support,dc=com
User Name: cn=kishore
Attributes: {userpassword=userPassword: [B@de8a1be5, uid=uid: kishore, objectclass=objectClass: person, top, uidObject, sn=sn: 123123123, cn=cn: kishore}
-------------------------------------
5)
User Distinguished Name: cn=performer,ou=resources,dc=support,dc=com
User Name: cn=performer
Attributes: {userpassword=userPassword: [B@57eddb22, uid=uid: performer, objectclass=objectClass: uidObject, person, top, sn=sn: 987654, cn=cn: performer}


More user records matched to filter (sn=*). But output is limited to 5 records based on SEARCH_CONTROL_COUNT_LIMIT property
