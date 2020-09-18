# UEFI HTTPS Boot

The security of HTTPS boot is that of the underlying Transport Layer Security (TLS). In simple terms, HTTPS boot refers to the use of HTTP boot over TLS session. So, please refer to the [UEFI HTTP Boot](https://github.com/tianocore/tianocore.github.io/wiki/HTTP-Boot) page for details. This page includes links to notes for HTTP/HTTPS server configuration and 3rd party documentation.

## HTTPS Boot Getting Started

Please refer to the [EDK II HTTPS Boot Getting Started Guide](https://edk2-docs.gitbook.io/getting-started-with-uefi-https-boot-on-edk-ii) for a step by step guide of the HTTPS Boot enabling and HTTPS server configuration.

## HTTPS Boot Authentication

TLS supports three authentication modes ([RFC5246](https://tools.ietf.org/html/rfc5246)):

```
1. Total anonymity: the server and client will not authenticate each other.
2. One-way authentication: server authentication with an unauthenticated client.
3. Two-way authentication: authentication of both parties.
```

Currently, the UEFI HTTPS boot feature only support server authentication with an unauthenticated client mode. Others are not in our current feature support scope. To support one-way authentication mode, server CA certificate is required by Client. Private variable is used to configure this CA certificate. **EFI_SIGNATURE_LIST** format is used for this variable. In sum, the Server CA certificate must be configured first to enable HTTPS boot feature. The variable name and GUID are defined as below.

```
#define EFI_TLS_CA_CERTIFICATE_GUID \
  { \
    0xfd2340D0, 0x3dab, 0x4349, { 0xa6, 0xc7, 0x3b, 0x4f, 0x12, 0xb4, 0x8e, 0xae } \
  }

#define EFI_TLS_CA_CERTIFICATE_VARIABLE          L"TlsCaCertificate"
```

**TlsAuthConfigDxe** is the driver providing the UI support for certificate configuration.

## HTTPS Boot Support Scope

* Feature usage: Load the file specified in the URI from a HTTP/HTTPS server.
* UEFI Arch: IA32 and X64 platform.
* TLS version: TLS 1.0/1.1/1.2, version negotiation.
* HTTPS authentication mode: One-way authentication.
* CA certificates management: Private variable, requires runtime variable security.

## HTTPS Boot Verification

Tomcat, IIS 8 and Apache2 are selected as the HTTPS server to verify the result of loading the UEFI shell boot file (Shell.efi) in NT32 platform, detailed see below table.

| HTTPS Server | TLS 1.0 | TLS 1.1 | TLS 1.2 |
|:------------:|:-------:|:-------:|:------:|
|Tomcat | Pass |Pass | Pass |
|IIS 8 | Pass | Pass | Failure |
|Apache2 | Pass | Pass | Pass |

### Configuration Notes

TLS version 1.2 in Microsoft Windows Server 2012 R2 IIS8 (functioning as an HTTPS server) MAY NOT be able to collaborate with a UEFI HTTPS boot client, while version 1.1/1.0 works well. To make sure the UEFI HTTPS boot client works properly, disable TLS version 1.2 using the PowerShell script shown below:

```
New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value 0 -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
Write-Host 'TLS 1.2 server has been disabled.'
Write-Host 'NOTE: Need to restart the OS to take effect!'
```
