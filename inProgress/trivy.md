## trivy ##

It is crucial to scan for vulnerabilities, and there are numerous tools and utilities available, and `Trivy` is one of the most convinient tool to help you with this.

Trivy is a command-line vulnerability scanner that can do much more than simply scan images. It can help you scan your config files for errors, scan the filesystem, scan your local and remote repositories, and, most importantly, scan your K8s cluster (though this is an experimental feature as of this writing).

It's installation is pretty straight forward, ref https://aquasecurity.github.io/trivy => Installation for more details.

If you are using Ubuntu as I am, follow below steps:
```
sudo apt-get install wget gnupg
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

Just a quick overview of Trivy. It is intended to quickly and easily detect known vulnerabilities in your platform, and it can be integrated into your CI/CD pipeline to ensure that vulnerabilities are discovered and addressed early in the development process. It is a free and open-source software that can run on a variety of operating systems such as Linux, macOS, and Windows.

Though running trivy is simple and only requires one command, one frequently asked question is how trivy works. To give you a quick overview, trivy follows the following process in the backend:
- First comes `image scanning`, Trivy starts by scanning the container image layer by layer. It examines the filesystem, installed packages and libraries.
- Trivy then consults a `vulnerability database` to identify known vulnerabilities associated with the image's packages and libraries. This database is managed by 'aquasecurity', but it is enriched and curated by multiple other databases such as NVD, RHSA, and others.
- Once Trivy has compiled a list of vulnerabilities, it evaluates each one to determine the severity and likelihood of exploitation. The CVSS score and the vulnerability description are used in this `assessment`.
- Trivy then generates a `report` containing information about the vulnerabilities discovered in the container image, such as their severity level, the affected package or library, and remediation recommendations.


Here are some examples of how to use Trivy:

- Scan a local container image: `trivy image IMAGE:TAG`, in below example `nginx.stubs:latest` this is a local image.
```
$ trivy image nginx.stubs:latest
2024-11-18T10:10:52Z    INFO    [vuln] Vulnerability scanning is enabled
2024-11-18T10:10:52Z    INFO    [secret] Secret scanning is enabled
2024-11-18T10:10:52Z    INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2024-11-18T10:10:52Z    INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.57/docs/scanner/secret#recommendation for faster secret detection
2024-11-18T10:10:52Z    INFO    Detected OS     family="debian" version="12.8"
2024-11-18T10:10:52Z    INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=149
2024-11-18T10:10:52Z    INFO    Number of language-specific files       num=0
2024-11-18T10:10:52Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://aquasecurity.github.io/trivy/v0.57/docs/scanner/vulnerability#severity-selection for details.

nginx.stubs:latest (debian 12.8)

Total: 144 (UNKNOWN: 0, LOW: 98, MEDIUM: 28, HIGH: 16, CRITICAL: 2)

┌────────────────────┬─────────────────────┬──────────┬──────────────┬─────────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│      Library       │    Vulnerability    │ Severity │    Status    │    Installed Version    │ Fixed Version │                            Title                             │
├────────────────────┼─────────────────────┼──────────┼──────────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
```

- Scan a remote container image: `trivy image REPO/IMAGE:TAG`, in below example `docker.io/library/nginx:latest` this is a image referring docker registry.
```
$ trivy image docker.io/library/nginx:latest
2024-11-18T10:11:47Z    INFO    [vuln] Vulnerability scanning is enabled
2024-11-18T10:11:47Z    INFO    [secret] Secret scanning is enabled
2024-11-18T10:11:47Z    INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2024-11-18T10:11:47Z    INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.57/docs/scanner/secret#recommendation for faster secret detection
2024-11-18T10:11:47Z    INFO    Detected OS     family="debian" version="12.8"
2024-11-18T10:11:47Z    INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=149
2024-11-18T10:11:47Z    INFO    Number of language-specific files       num=0
2024-11-18T10:11:47Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://aquasecurity.github.io/trivy/v0.57/docs/scanner/vulnerability#severity-selection for details.

docker.io/library/nginx:latest (debian 12.8)

Total: 144 (UNKNOWN: 0, LOW: 98, MEDIUM: 28, HIGH: 16, CRITICAL: 2)

┌────────────────────┬─────────────────────┬──────────┬──────────────┬─────────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│      Library       │    Vulnerability    │ Severity │    Status    │    Installed Version    │ Fixed Version │                            Title                             │
├────────────────────┼─────────────────────┼──────────┼──────────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
```

- Scan a local directory: `trivy filesystem FS_PATH`, here I have intentionally kept a test `private key` in my /tmp directory just for testing purposes.
```
$ trivy filesystem /tmp/
2024-11-18T10:14:07Z    INFO    [vuln] Vulnerability scanning is enabled
2024-11-18T10:14:07Z    INFO    [secret] Secret scanning is enabled
2024-11-18T10:14:07Z    INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2024-11-18T10:14:07Z    INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.57/docs/scanner/secret#recommendation for faster secret detection
2024-11-18T10:14:07Z    INFO    Number of language-specific files       num=0

ops.io (secrets)

Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 1, CRITICAL: 0)

HIGH: AsymmetricPrivateKey (private-key)
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
Asymmetric Private Key
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
 ops.io:1
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1 [ BEGIN OPENSSH PRIVATE KEY-----******************************************************************************************************************************************************************************************************************************************************************************************************************************************-----END OPENSSH PRI
   2
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

```

- Download trivy database (can be used for automation - if required), you can use `trivy image --download-db-only --cache-dir PATH_FOR_DB`
```
$ trivy image --download-db-only --cache-dir /tmp/trivy-db
2024-11-18T10:14:52Z    INFO    [vulndb] Need to update DB
2024-11-18T10:14:52Z    INFO    [vulndb] Downloading vulnerability DB...
2024-11-18T10:14:52Z    INFO    [vulndb] Downloading artifact...        repo="ghcr.io/aquasecurity/trivy-db:2"
55.65 MiB / 55.65 MiB [---------------------------------------------------------------------------------------------------------------------------------------------------------------] 100.00% 4.98 MiB p/s 11s
2024-11-18T10:15:04Z    INFO    [vulndb] Artifact successfully downloaded       repo="ghcr.io/aquasecurity/trivy-db:2"
```

- Scan with a specific vulnerability database:
```
$ trivy image --cache-dir /tmp/trivy-db nginx
2024-11-18T10:18:38Z    INFO    [vuln] Vulnerability scanning is enabled
2024-11-18T10:18:38Z    INFO    [secret] Secret scanning is enabled
2024-11-18T10:18:38Z    INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2024-11-18T10:18:38Z    INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.57/docs/scanner/secret#recommendation for faster secret detection
2024-11-18T10:18:38Z    INFO    Detected OS     family="debian" version="12.8"
2024-11-18T10:18:38Z    INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=149
2024-11-18T10:18:38Z    INFO    Number of language-specific files       num=0
2024-11-18T10:18:38Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://aquasecurity.github.io/trivy/v0.57/docs/scanner/vulnerability#severity-selection for details.

nginx (debian 12.8)

Total: 144 (UNKNOWN: 0, LOW: 98, MEDIUM: 28, HIGH: 16, CRITICAL: 2)

┌────────────────────┬─────────────────────┬──────────┬──────────────┬─────────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│      Library       │    Vulnerability    │ Severity │    Status    │    Installed Version    │ Fixed Version │                            Title                             │
├────────────────────┼─────────────────────┼──────────┼──────────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
```

- Scan and output results in JSON format:
```
$ trivy --format json image nginx
```

- Scan an image and only list vulnerabilities with specific severity levels. In below example we are just looking for CRITICAL vulnerabilities and see the number reduced to 2.
```
$ trivy image --severity CRITICAL nginx
2024-11-18T10:21:15Z    INFO    [vuln] Vulnerability scanning is enabled
2024-11-18T10:21:15Z    INFO    [secret] Secret scanning is enabled
2024-11-18T10:21:15Z    INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2024-11-18T10:21:15Z    INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.57/docs/scanner/secret#recommendation for faster secret detection
2024-11-18T10:21:15Z    INFO    Detected OS     family="debian" version="12.8"
2024-11-18T10:21:15Z    INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=149
2024-11-18T10:21:15Z    INFO    Number of language-specific files       num=0
2024-11-18T10:21:15Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://aquasecurity.github.io/trivy/v0.57/docs/scanner/vulnerability#severity-selection for details.

nginx (debian 12.8)

Total: 2 (CRITICAL: 2)

┌─────────┬────────────────┬──────────┬──────────────┬───────────────────┬───────────────┬────────────────────────────────────────────────────────┐
│ Library │ Vulnerability  │ Severity │    Status    │ Installed Version │ Fixed Version │                         Title                          │
├─────────┼────────────────┼──────────┼──────────────┼───────────────────┼───────────────┼────────────────────────────────────────────────────────┤
│ libaom3 │ CVE-2023-6879  │ CRITICAL │ affected     │ 3.6.0-1+deb12u1   │               │ aom: heap-buffer-overflow on frame size change         │
│         │                │          │              │                   │               │ https://avd.aquasec.com/nvd/cve-2023-6879              │
├─────────┼────────────────┤          ├──────────────┼───────────────────┼───────────────┼────────────────────────────────────────────────────────┤
│ zlib1g  │ CVE-2023-45853 │          │ will_not_fix │ 1:1.2.13.dfsg-1   │               │ zlib: integer overflow and resultant heap-based buffer │
│         │                │          │              │                   │               │ overflow in zipOpenNewFileInZip4_6                     │
│         │                │          │              │                   │               │ https://avd.aquasec.com/nvd/cve-2023-45853             │
└─────────┴────────────────┴──────────┴──────────────┴───────────────────┴───────────────┴────────────────────────────────────────────────────────┘
```

- There may be times when you want to ignore unfixed vulnerabilities in order to prioritise fixing other vulnerabilities. Then '--ignore-unfixed' is your best friend. In the example below, notice how the total number has been reduced to 0.
```
$ trivy image --severity CRITICAL --ignore-unfixed nginx
2024-11-18T10:21:49Z    INFO    [vuln] Vulnerability scanning is enabled
2024-11-18T10:21:49Z    INFO    [secret] Secret scanning is enabled
2024-11-18T10:21:49Z    INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2024-11-18T10:21:49Z    INFO    [secret] Please see also https://aquasecurity.github.io/trivy/v0.57/docs/scanner/secret#recommendation for faster secret detection
2024-11-18T10:21:49Z    INFO    Detected OS     family="debian" version="12.8"
2024-11-18T10:21:49Z    INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=149
2024-11-18T10:21:49Z    INFO    Number of language-specific files       num=0
2024-11-18T10:21:49Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://aquasecurity.github.io/trivy/v0.57/docs/scanner/vulnerability#severity-selection for details.

nginx (debian 12.8)

Total: 0 (CRITICAL: 0)
```

Note:
- When Trivy first starts up, it downloads its vulnerability database then it renews it roughly every 12 hours.
- Usually the process is pretty fast, since the database is pretty light weight, but if need be this can be skipped as well by using `--skip-update` option while running the scan.


References: 
- https://aquasecurity.github.io/trivy
- https://github.com/aquasecurity/trivy
- `trivy --help`, there are plenty of subcommands available for trivy, here is a list of them which is fetched from mentioned command:
  ```
  Available Commands:
  completion  Generate the autocompletion script for the specified shell
  config      Scan config files for misconfigurations
  filesystem  Scan local filesystem
  help        Help about any command
  image       Scan a container image
  kubernetes  [EXPERIMENTAL] Scan kubernetes cluster
  module      Manage modules
  plugin      Manage plugins
  repository  Scan a remote repository
  rootfs      Scan rootfs
  sbom        Scan SBOM for vulnerabilities
  server      Server mode
  version     Print the version
  vm          [EXPERIMENTAL] Scan a virtual machine image
  ```
