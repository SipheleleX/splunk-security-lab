# Contributing to Splunk Security Lab

Thank you for your interest in contributing! This document provides guidelines for contributing to this project.

## How to Contribute

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/YourFeature`)
3. **Make your changes**
4. **Test thoroughly**
5. **Commit your changes** (`git commit -m 'Add YourFeature'`)
6. **Push to your fork** (`git push origin feature/YourFeature`)
7. **Open a Pull Request**

## Pull Request Guidelines

- Provide a clear description of the changes
- Include screenshots for UI changes
- Update documentation as needed
- Ensure all containers start successfully
- Test attack simulations work correctly

## Reporting Issues

When reporting issues, please include:
- Your operating system
- Docker version
- Steps to reproduce
- Expected vs actual behavior
- Relevant log output

## Questions?

Feel free to open an issue for any questions or suggestions!
EOF

# Create detailed SPL guide
cat > SPL-GUIDE.md << 'EOF'
# Splunk Processing Language (SPL) Guide

## Essential Commands for Security Analysis

### Search Commands

**Basic search**:
```spl
index=main sourcetype=_json
```

**Search with filters**:
```spl
index=main sourcetype=_json attack_type="sql_injection" severity="critical"
```

**Time range search**:
```spl
index=main earliest=-1h latest=now
```

### Statistical Commands

**Count events**:
```spl
index=main | stats count
```

**Count by field**:
```spl
index=main | stats count by attack_type
```

**Multiple statistics**:
```spl
index=main | stats count, avg(field), max(field), min(field) by attack_type
```

**Distinct count**:
```spl
index=main | stats dc(attack_type) as unique_attacks
```

### Time-based Analysis

**Timechart**:
```spl
index=main | timechart span=5m count by attack_type
```

**Trend analysis**:
```spl
index=main | timechart span=1h count | predict count
```

### Filtering & Transformation

**Where clause**:
```spl
index=main | where severity="critical" AND attack_type="sql_injection"
```

**Eval (create new fields)**:
```spl
index=main | eval risk_score=if(severity="critical",10,5)
```

**Table output**:
```spl
index=main | table _time, attack_type, severity, target, payload
```

### Advanced Queries

**Top values**:
```spl
index=main | top limit=10 attack_type
```

**Rare values**:
```spl
index=main | rare attack_type
```

**Transaction analysis**:
```spl
index=main | transaction target maxspan=5m
```

**Subsearches**:
```spl
index=main attack_type=[search index=main severity="critical" | return attack_type]
```

## Security-Specific Queries

### Threat Hunting

**Find potential data exfiltration**:
```spl
index=main 
| stats sum(bytes_out) as total_bytes by src_ip 
| where total_bytes > 1000000
```

**Detect brute force attempts**:
```spl
index=main attack_type="brute_force" 
| stats count by target 
| where count > 5
```

**Identify attack chains**:
```spl
index=main 
| transaction target maxspan=10m 
| where eventcount > 3
```

### Reporting

**Executive summary**:
```spl
index=main sourcetype=_json 
| stats count as "Total Attacks", 
        count(eval(severity="critical")) as "Critical", 
        count(eval(severity="high")) as "High" 
| eval "Risk Score"=Critical*10 + High*5
```

**Daily attack report**:
```spl
index=main 
| bucket _time span=1d 
| stats count by _time, attack_type 
| sort -_time
```

## Best Practices

1. **Always specify index** for better performance
2. **Use time ranges** to limit search scope
3. **Filter early** in the search pipeline
4. **Use stats over transaction** when possible
5. **Test queries** on small time ranges first

## Performance Tips

- Use `tstats` for faster searches on indexed fields
- Limit returned fields with `fields` command
- Use summary indexing for frequently-run searches
- Leverage data models when available

## Resources

- [Official SPL Reference](https://docs.splunk.com/Documentation/Splunk/latest/SearchReference)
- [SPL Cheat Sheet](https://www.splunk.com/pdfs/solution-guides/splunk-quick-reference-guide.pdf)
