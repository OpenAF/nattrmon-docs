---
layout: default
title: Output in HTTPs with basic authentication
parent: Medium
grand_parent: Guides
---

# Output in HTTPs with basic authentication

_since nAttrMon version 20210318_

If you need to deploy some very basic user authentication for all (or almost all) HTTP communication each of the nOutput_HTTP_* plugs support basic authentication based on a list of static users or by executing a custom function (e.g. authenticating through a LDAP/Active Directory for example).

The best example is located in _outputs.disabled/yaml/00.httpAll.yaml_:

````yaml
common: &COMMON
  #keyStore   : /some/folder/ssl.jks
  #keyPassword: 797AD06F0FB6E1E6F4B17EB182670494D3EA259B24245847062CCA0C5D023F26
  authType : basic
  authLocal:
    nattrmon: 
      p: nattrmon
      m: r
    change:
      p: me 
      m: rw
  #authCustom: |
  #   // Custom has priority over local. Comment the entry you won't use.
  #   //
  #   // u - user
  #   // p - password
  #   // s - server object
  #   // r - request object (e.g. uri, method, header["remote-addr"], header["user-agent"], ...)
  #         
  #   // Forced user & password
  #   if (u == "nattrmon" && p == "nattrmon") return true;
  #
  #   // Users from a ldap or active directory
  #   try {
  #     new ow.server.ldap("ldap://my.auth.ldap:389", u + "@my.domain", p);
  #     return true;
  #   } catch(e) {
  #     tlogErr("AUDIT | {{user}} authentication refused ({{message}}).", { user: u, message: e.message });
  #   }
  #
  #   return false;

output:
  # -----------------------------
  - name         : Output HealthZ
    description  : Exposes a /healthz, /livez and /readyz endpoints
    execFrom     : nOutput_HTTP_HealthZ
    execArgs     :
    #  <<            : *COMMON
    #  includeHealthZ: true
    #  includeLiveZ  : true
    #  includeReadyZ : true

  # --------------------------
  - name         : Output JSON
    description  : Outputs attributes, warnings, current and previous values in a JSON representation through HTTP.
    execFrom     : nOutput_HTTP_JSON
    execArgs     : *COMMON

  # --------------------------
  - name         : Output HTTP
    description  : >
      Provides a very basic web site for read-only access to the inputs and warnings generated. It uses the nOutput_HTTPJSON to
      retrieve the necessary data when needed.
    execFrom     : nOutput_HTTP
    execArgs     : 
      <<   : *COMMON
      title: Some title

  # ------------------------------
  - name         : Output Channels
    description  : |
      Provides, on the same nOutput_HTTPJSON or nOutput_HTTP port or other, access to the nAttrMon specific OpenAF channels like: /chs/ops,
      /chs/cvals, /chs/pvals and /chs/attr.
    execFrom     : nOutput_Channels
    execArgs     : *COMMON

  # -----------------------------
  - name         : Output Metrics
    description  : Outputs attributes, warnings, current and previous values in a JSON representation through HTTP.
    execFrom     : nOutput_HTTP_Metrics
    execArgs     : *COMMON
````

Notes about this example:
* the authentication details are shared using YAML anchors between all output plugs
* the HealthZ output plug doesn't include the authentication details on propose since it doesn't contain more information other than operational healthz/livez/readyz.
* this examples is a [multiple plugs in one file](../advanced/nattrmon-multiple-plugs-one-file.md), so if you have other output plugs for the same outputs you need to remove them (and include any execArgs, if any, in this main example).

The following table provides a description of each execArgs common to all output plugs:

| execArg | Mandatory? | Description |
|---------|------------|-------------|
| authType | Yes | Currently only "basic" is supported and it's based on the browser's basic authentication mechanism. |
| authLocal | No | This is a map containing static user credentials and permissions. |
| authLocal._user_ | Yes | Each map entry will be a static defined user. |
| authLocal._user_.p | Yes | For each static defined user this map entry will contain the password (encrypted passwords are supported) | 
| authLocal._user_.m | No | This are the modification permissions used currently for the [nOutput_Channels](../../reference/outputs/nAttrMon-nOutput-Channels.md) plug ("r" for read-only (default if ommited) and "rw" for read-write). |
| authCustom | No | Let's you use an OpenAF javascript function to customize authentication and authorization. The function will receive 4 parameters: u - the username provided, p - the password provided, s - a HTTPServer OpenAF object and r - a request object. The function can set r.channelPermission to customize authorization ("r" or "rw") for the [nOutput_Channels](../../reference/outputs/nAttrMon-nOutput-Channels.md) plug. If the function returns true the user will be authenticated otherwise access will be denied. Please refer to the example (that performs a simple LDAP authentication) for more details. |

## Security considerations

In terms of security since the user credentials will be sent by the browser on every request you should use it only with HTTPs enabled (enabling _keyStore_ and the _keyPassword_ execArgs). The code can have security issues and just provides a very basic user validation mechanism. If you need strong security you should remove the corresponding output plugs or block access to non-secure networks and only permit access with a secure reverse proxy. Keep also in mind that all HTTP based outputs also support the _host_ execArgs to bind the HTTP/HTTPs listening port to a specific network interface. 

## Audit

As with the usual HTTP output audit logs when an user is authenticated the audit logs will also include the user name with every request. Wrong authentications attemps will also be logged except for _authCustom_ where your custom function should perform the authentication audit logging as exemplified.

See more details about HTTPs outputs in [Output in HTTPs](output_https_withBasicAuth.md).

