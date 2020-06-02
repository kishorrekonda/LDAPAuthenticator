LDAP Authenticator, An utility to perform authentication to LDAP server

Description
Idea behind creating this utility is to simplify troubleshooting of LDAP integration/authentication issues within IBM Sterling B2B Integrator (B2Bi) product. Here is documentation how LDAP server is integrated with B2Bi -

Some of challenges to troubleshoot LDAP authentication issues B2Bi being LDAP client
    LDAP authentication policy changes in B2Bi requires application restart.
    Capturing ssl traces for LDAP connections requires application restart
    B2Bi's ssl traces printed in one single log file, makes it hard to review traces of one single connection

How does this work?
There are 3 stages in the way it works.
    Connects to LDAP Server over host and port. Then gets access to LDAP tree by authenticating through LDAP BIND admin credentials.
    Search for login user, through search filter such as (uid=<userid>), under search root.
    Once login user is found as valid authorized user, goes onto authentication of the same user by user's DN (Distinguished Name). This is where user would be prompted to enter password.

What does this this tool offer?
    It works like any typical LDAP client supports both LDAP as well as LDAPS.
    Allows to capture java ssl traces.
    Supports multiple security protocols - TLSv1, TLSv1.1 and TLSv1.2.
    Supports complex search filters for user search under search root.
    Able to list attributes associated with username being searched for.

Should work with any LDAP Server. Successfully tested with OpenLDAP and Active Directory.

Download 
https://github.com/kishorrekonda/LDAPAuthenticator/blob/master/LDAPAuthenticator.zip

Usage
Extract LDAPAuthenticator.zip to get LDAPAuthenticator.jar and sample input file. You would have to replace sample values for all the properties with real values received from your LDAP server before executing following command.

jdk/bin/java -jar LDAPAuthenticator.jar [location-of-properties-file]

Note - If you are using this tool for B2Bi product, prefer to use jdk under <b2b_install_home>
i.e., <b2b_install_home>/jdk/bin/java -jar LDAPAuthenticator.jar [location-of-properties-file]

Configure following properties with respective values as input file
LDAP_SERVER_HOST = [host]
LDAP_SERVER_PORT = [port]
LDAP_SEARCH_ROOT = [ldap_search_root]
LDAP_BIND_PRINCIPAL = [ldap_admin_user]
LDAP_BIND_PASSWORD = [ldap_admin_password]
LDAP_SEARCH_FILTER = (uid=<userid>)
LDAP_LOGIN_USERID = [ldap-login-username]
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
LDAP_SEARCH_FILTER* 		Template to use in the search. It must contain <userid> place holder so value is dynamically replaced at request time with the actual username of the user being authenticated.
							sample: (uid=<userid>)
							sample: (&(uid=<userid>)(cn=<userid>))
LDAP_LOGIN_USERID* 			username of user being authenticated
LDAP_IS_SECURE 				Specify whether it is TLS/SSL connection. Yes or No. Default is No
LDAP_TRUST_STORE 			File path to LDAP trust store (.jks file). Required if LDAP_IS_SECURE=Yes
LDAP_TRUST_STORE_PASS 		Password that allows access to LDAP_TRUST_STORE. Required if LDAP_TRUST_STORE is specified
LDAP_KEY_STORE 				File path to LDAP client key store (.jks file). Required if LDAP_IS_SECURE=Yes and client SSL/TLS authentication is enabled
LDAP_KEY_STORE_PASS 		Password that allows access to LDAP_KEY_STORE. Required if LDAP_KEY_STORE is specified
LDAP_SSL_DEBUG 				Tells if SSL traces are needed. Valid values are Yes or No
LDAP_TLS_VERSION 			Specify TLS protocol to be used. Valid values are TLSv1, TLSv1.1, TLSv1.2 or their combination such as TLSv1-TLSv1.2. Default is TLSv1.2. It is used if LDAP_IS_SECURE=Yes
SEARCH_CONTROL_COUNT_LIMIT 	Number of user records to be pulled during userid search (This is used during stage#2 of tool execution)
SEARCH_CONTROL_TIME_LIMIT 	Timeout in milli-sec

Sample execution of tool:
    $$ b2b_install/jdk/bin/java -jar LDAPAuthenticator.jar ldap.properties
    #########
    Reading property file..
    Input configuration: {SEARCH_CONTROL_TIME_LIMIT=15000, LDAP_BIND_PASSWORD=secret, SEARCH_CONTROL_COUNT_LIMIT=50, LDAP_SEARCH_ROOT=ou=resources,dc=services,dc=com, LDAP_SERVER_HOST=ldap-host-ip, LDAP_SSL_DEBUG=No, LDAP_BIND_PRINCIPAL=cn=Manager,dc=services,dc=com, LDAP_TRUST_STORE=/tmp/TrustStore.jks, LDAP_SEARCH_FILTER=(&(cn=)(cn=)), LDAP_TRUST_STORE_PASS=password, LDAP_SERVER_PORT=389, LDAP_TLS_VERSION=TLSv1-TLSv1.1, LDAP_LOGIN_USERID=kishore}
    Reading property file complete.
    ########
    Making LDAP connection by service user...
    {java.naming.provider.url=ldap://ldap-host-ip:389, java.naming.factory.initial=com.sun.jndi.ldap.LdapCtxFactory, java.naming.security.principal=cn=Manager,dc=services,dc=com, java.naming.security.authentication=simple, java.naming.security.credentials=secret, com.sun.jndi.ldap.read.timeout=15000}
    Successful connection
    #########
    Searching for user record on LDAP server....
    Search Filter: (&(cn=kishore)(cn=kishore))
    User search Successful! Means user is authorized.
    User Attributes :: {userpassword=userPassword: [B@6e8ea4c7, uid=uid: kishore, objectclass=objectClass: person, top, uidObject, sn=sn: 123123123, cn=cn: kishore}
    User Distinguished Name: cn=kishore,ou=resources,dc=services,dc=com
    User Name: cn=kishore
    ########
    Would you like to test authentication? (Y/N): y
    Going ahead with Authentication...
    Enter login password of user 'kishore' :
    Password entered by user: password
    Authenticating User by this configuration: {java.naming.provider.url=ldap://ldap-host-ip:389, java.naming.factory.initial=com.sun.jndi.ldap.LdapCtxFactory, java.naming.security.principal=cn=kishore,ou=resources,dc=services,dc=com, java.naming.security.authentication=simple, java.naming.security.credentials=password, com.sun.jndi.ldap.read.timeout=15000}
    'kishore' is authenticated succesfully.
