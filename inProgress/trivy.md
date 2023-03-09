## trivy ##

It is critical to scan for vulnerabilities, and there are numerous tools and utilities available today, my favourite of which is Trivy.

Trivy is a command-line vulnerability scanner that can do much more than simply scan containers or Docker images. It can help you scan your config files for errors, scan the filesystem, scan your local and remote repositories, and, most importantly, scan your K8s cluster (though this is an experimental feature as of this writing).

Just a quick overview of Trivy. It is intended to quickly and easily detect known vulnerabilities in your platform, and it can be integrated into your CI/CD pipeline to ensure that vulnerabilities are discovered and addressed early in the development process. It is free and open-source software that can run on a variety of operating systems such as Linux, macOS, and Windows.

Though running trivy is simple and only requires one command, one frequently asked question is how trivy works. To give you a quick overview, trivy follows the following process in the backend:
- First comes `image scanning`, Trivy starts by scanning the container image layer by layer. It examines the filesystem, installed packages and libraries.
- Trivy then consults a `vulnerability database` to identify known vulnerabilities associated with the image's packages and libraries. This database is managed by 'aquasecurity', but it is enriched and curated by multiple other databases such as NVD, RHSA, and others.
- Once Trivy has compiled a list of vulnerabilities, it evaluates each one to determine the severity and likelihood of exploitation. The CVSS score and the vulnerability description are used in this `assessment`.
- Trivy then generates a `report` containing information about the vulnerabilities discovered in the container image, such as their severity level, the affected package or library, and remediation recommendations.


Here are some examples of how to use Trivy:

- Scan a local container image: `trivy image IMAGE:TAG`, in below example `nginx.stubs:latest` this is a local image.
```
$ trivy image nginx.stubs:latest
2023-03-09T16:06:38.582+0530	INFO	Vulnerability scanning is enabled
2023-03-09T16:06:38.582+0530	INFO	Secret scanning is enabled
2023-03-09T16:06:38.582+0530	INFO	If your scanning is slow, please try '--security-checks vuln' to disable secret scanning
2023-03-09T16:06:38.582+0530	INFO	Please see also https://aquasecurity.github.io/trivy/v0.36/docs/secret/scanning/#recommendation for faster secret detection
2023-03-09T16:06:38.595+0530	INFO	Detected OS: debian
2023-03-09T16:06:38.595+0530	INFO	Detecting Debian vulnerabilities...
2023-03-09T16:06:38.629+0530	INFO	Number of language-specific files: 0

nginx.stubs:latest (debian 11.4)

Total: 187 (UNKNOWN: 0, LOW: 86, MEDIUM: 50, HIGH: 42, CRITICAL: 9)

Output trimmed, Expected output is a list of all vulnerabilities in the format shown below.

┌──────────────┬─ ─────────────┬──────────┬────────────────────┬─────────────────┬───────────────────────────────┐
│   Library    │ Vulnerability │ Severity │  Installed Version │  Fixed Version  │            Title              │
├──────────────┼── ────────────┼──────────┼────────────────────┼─────────────────┼───────────────────────────────┤

```

- Scan a remote container image: `trivy image REPO/IMAGE:TAG`, in below example `docker.io/library/nginx:latest` this is a image referring docker registry.
```
$ trivy image docker.io/library/nginx:latest
2023-03-09T16:07:12.656+0530	INFO	Vulnerability scanning is enabled
2023-03-09T16:07:12.656+0530	INFO	Secret scanning is enabled
2023-03-09T16:07:12.656+0530	INFO	If your scanning is slow, please try '--security-checks vuln' to disable secret scanning
2023-03-09T16:07:12.656+0530	INFO	Please see also https://aquasecurity.github.io/trivy/v0.36/docs/secret/scanning/#recommendation for faster secret detection
2023-03-09T16:07:12.664+0530	INFO	Detected OS: debian
2023-03-09T16:07:12.664+0530	INFO	Detecting Debian vulnerabilities...
2023-03-09T16:07:12.675+0530	INFO	Number of language-specific files: 0

docker.io/library/nginx:latest (debian 11.4)

Total: 185 (UNKNOWN: 0, LOW: 86, MEDIUM: 50, HIGH: 40, CRITICAL: 9)

Output trimmed, Expected output is a list of all vulnerabilities in the format shown below.

┌──────────────┬─ ─────────────┬──────────┬────────────────────┬─────────────────┬───────────────────────────────┐
│   Library    │ Vulnerability │ Severity │  Installed Version │  Fixed Version  │            Title              │
├──────────────┼── ────────────┼──────────┼────────────────────┼─────────────────┼───────────────────────────────┤

```

Scan a local directory: `trivy filesystem FS_PATH`, here I have kept a test private key in my /tmp directory just for testing purposes.
```
$ trivy filesystem /tmp
2023-03-09T16:08:26.699+0530	INFO	Vulnerability scanning is enabled
2023-03-09T16:08:26.699+0530	INFO	Secret scanning is enabled
2023-03-09T16:08:26.699+0530	INFO	If your scanning is slow, please try '--security-checks vuln' to disable secret scanning
2023-03-09T16:08:26.699+0530	INFO	Please see also https://aquasecurity.github.io/trivy/v0.36/docs/secret/scanning/#recommendation for faster secret detection
2023-03-09T16:08:26.702+0530	INFO	Number of language-specific files: 0

hola (secrets)

Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 1, CRITICAL: 0)

HIGH: AsymmetricPrivateKey (private-key)
════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
Asymmetric Private Key
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
 hola:1
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1 [ -----BEGIN OPENSSH PRIVATE KEY-----*********************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************-----END OPENSSH PRIVATE KEY-----
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
```

Scan multiple images at once:
```
$ trivy image nginx-test:v1 nginx-test:v2
```

Scan with a specific vulnerability database:
```
$ trivy --db-path /opt/vuln_db image nginx-test:v1
```

Scan and output results in JSON format:
```
$ trivy --format json image nginx-test:v1
```
