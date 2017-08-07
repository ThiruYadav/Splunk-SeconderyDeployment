# Splunk-SeconderyDeployment
Splunk SeconderyDeployment Code Steps:

1) Make SeconderyDeployment poll to Primary deployment server----verfie by the command "splunk cmd btool deploymentclient list"
2) Verfiy that Splunk Secondary deployment server is able to communicate with Primary deployment server--telnet Primary Deployment server:8089
3) On the SecondryDeployment create the directories to obtain this path: "$SPLUNK_HOME\etc\auth\certs\" and place the new .pem file in the certs directory
4) SecondaryDeployment server by following the below steps.
	a) Go to the path, "$SPLUNK_HOME\etc\system\local\deploymentclient.conf"
		[deployment-client]
		reloadDSOnAppInstall = true
		
		[target-broker:deploymentServer]
		targetUri = splunkds.avanade.org:8089

	b) Go to the path, "$SPLUNK_HOME\etc\system\local\serverclass.conf"
		[serverClass:SecondaryDeploymentServer]
		whitelist.0=SJRPSPLIF01
		[serverClass:SecondaryDeploymentServer:app:avn_dcs_cul_int_uf_outputs]
		targetRepositoryLocation = $SPLUNK_HOME/etc/apps
		
		[serverClass:SecondaryDeploymentApps]
		whitelist.0=SJRPSPLIF01
		stateOnClient = disabled
		targetRepositoryLocation = $SPLUNK_HOME/etc/deployment-apps

		[serverClass:SecondaryDeploymentApps:app:Secondary_DS_uf_client_inputs]
		[serverClass:SecondaryDeploymentApps:app:Secondary_DS_uf_client_outputs]

5) Enable receiving on the I-HF_DS by going to $SPLUNK_HOME/etc/system/local/inputs.conf
[splunktcp://9997]
disabled = 0
6) Inputs on clients of secondary deployment server would use the same certificate that is present in the certs directory of the I-HF.
7) Outputs on clients of secondary deployment server would use the certificate that is already in place. It has to be pushed by main deployment server.
8) Ensure that firewall is open for the following hosts and ip addresses. Considering that the data would be indexed to dcs indexers.
