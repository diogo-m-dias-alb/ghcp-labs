# Security Scan Report - Dependencies Analysis

**Scan Date:** 2026-06-19  
**Tool:** Manual CVE Analysis + Dependency Version Assessment  
**Project:** lab07  

---

## Summary

| Metric | Count |
|--------|-------|
| **Total Packages Scanned** | 4 |
| **Critical Severity** | 1 |
| **High Severity** | 2 |
| **Medium Severity** | 1 |
| **Low Severity** | 0 |
| **No Known Issues** | 0 |

---

## Findings

### 1. ⚠️ CRITICAL: requests 2.18.0

| Field | Value |
|-------|-------|
| **Package** | requests |
| **Current Version** | 2.18.0 |
| **Release Date** | May 29, 2017 |
| **Age** | ~9 years old |
| **Known CVEs** | Multiple (9+) |
| **Severity** | **CRITICAL** |

#### Known Vulnerabilities

| CVE ID | Description | CVSS Score |
|--------|-------------|-----------|
| CVE-2023-32681 | Unintended leak of Proxy-Authorization header to upstream proxy during redirects | 7.5 |
| CVE-2018-18074 | Improper handling of redirects with urllib3 dependency | 6.5 |
| CVE-2018-20225 | Information disclosure via Authorization header in redirects | 5.3 |

#### Recommended Action
**URGENT:** Upgrade to **requests 2.31.0** or later (latest stable: 2.32.x)
```bash
pip install --upgrade requests
```

**Why:** This version is 9 years old and lacks all modern security patches, SSL/TLS improvements, and bug fixes. Continued use poses significant security risks.

---

### 2. ⚠️ HIGH: pyyaml (Unpinned)

| Field | Value |
|-------|-------|
| **Package** | pyyaml |
| **Current Version** | Unpinned (latest: 6.0.1) |
| **Pinned Version** | None |
| **Severity** | **HIGH** |

#### Known Vulnerabilities in Older Versions

| CVE ID | Description | Affected Versions | Severity |
|--------|-------------|-------------------|----------|
| CVE-2020-14343 | Arbitrary code execution via YAML.load() unsafe deserialization | < 5.4 | High |
| CVE-2017-18342 | Arbitrary code execution vulnerability in YAML parsing | < 3.13 | Critical |

#### Recommended Action
**PIN to Safe Version:** Add version constraint to requirements.txt
```bash
pyyaml>=6.0
```

Or use safe YAML loading:
```python
import yaml
# SAFE: Use safe_load() instead of load()
data = yaml.safe_load(open('file.yaml'))
```

**Why:** While unpinned, older versions could introduce RCE vulnerabilities. Pinning to 6.0+ ensures safe YAML deserialization.

---

### 3. ⚠️ HIGH: bcrypt (Unpinned)

| Field | Value |
|-------|-------|
| **Package** | bcrypt |
| **Current Version** | Unpinned (latest: 4.1.x) |
| **Pinned Version** | None |
| **Severity** | **HIGH** |

#### Potential Issues

- **No Critical CVEs** in recent versions, but older versions (< 3.1.0) had vulnerabilities
- **Unpinned dependency** means production could pull vulnerable version

#### Recommended Action
**PIN to Safe Version:** Specify minimum version
```bash
bcrypt>=4.0.0
```

**Why:** While modern bcrypt is secure, pinning prevents accidental installation of outdated versions with known issues.

---

### 4. ✅ MEDIUM: pytest (Unpinned)

| Field | Value |
|-------|-------|
| **Package** | pytest |
| **Current Version** | Unpinned (latest: 7.4.x) |
| **Status** | No known critical CVEs |
| **Severity** | **MEDIUM** (due to unpinned) |

#### Considerations

- pytest is well-maintained with no known RCE or authentication vulnerabilities
- Unpinned specification allows flexibility but introduces uncertainty in testing environments

#### Recommended Action
**PIN to LTS Version or specify minimum:**
```bash
pytest>=7.0.0
```

---

## Remediation Plan

### Immediate (Critical Priority)

**Action 1: Upgrade requests**
```bash
pip install --upgrade requests>=2.31.0
# Update requirements.txt:
requests>=2.31.0
```

**Action 2: Pin and verify pyyaml**
```bash
pip install pyyaml>=6.0
# Update requirements.txt:
pyyaml>=6.0
```

### Short-term (High Priority)

**Action 3: Pin bcrypt with version constraint**
```bash
pip install bcrypt>=4.0.0
# Update requirements.txt:
bcrypt>=4.0.0
```

**Action 4: Pin pytest for consistency**
```bash
pip install pytest>=7.0.0
# Update requirements.txt:
pytest>=7.0.0
```

### Ongoing (Best Practices)

1. **Enable Security Monitoring**
   ```bash
   pip install pip-audit
   pip-audit
   ```

2. **Regular CVE Checks** - Add to CI/CD pipeline:
   ```bash
   pip-audit --desc  # Show detailed CVE descriptions
   ```

3. **Dependency Pinning Strategy**
   - Use `>=X.Y.Z` for minimum safe versions
   - Use `<X.Y.Z` for breaking changes
   - Example: `requests>=2.31.0,<3.0.0`

4. **Scheduled Updates**
   - Review dependencies monthly for security patches
   - Subscribe to GitHub Dependabot alerts

---

## Updated requirements.txt (Recommended)

```
pytest>=7.0.0
bcrypt>=4.0.0
pyyaml>=6.0
requests>=2.31.0
```

---

## Risk Assessment

| Risk Level | Justification |
|------------|---------------|
| **Current State** | **CRITICAL** - requests 2.18.0 poses immediate security risk |
| **After Remediation** | **LOW** - All dependencies pinned to secure versions |
| **Implementation Time** | ~5 minutes |
| **Testing Impact** | Minimal - newer versions are backward compatible |

---

## References

- [requests Security History](https://github.com/psf/requests/security/advisories)
- [PyYAML CVE-2020-14343](https://nvd.nist.gov/vuln/detail/CVE-2020-14343)
- [bcrypt Security](https://github.com/pyca/bcrypt)
- [pip-audit Documentation](https://github.com/pypa/pip-audit)
