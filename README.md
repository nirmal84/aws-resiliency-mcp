# AWS Resiliency MCP Plugin

An MCP plugin that configures 6 official AWS MCP servers from [`awslabs/mcp`](https://github.com/awslabs/mcp) for resiliency-focused architecture reviews. Works with Claude Code, Cursor, Windsurf, and any MCP-compatible editor.

**Companion to [aws-resiliency-skill](https://github.com/nirmal84/aws-resiliency-skill)** — the skill provides the resiliency expertise (what to look for, failure modes, blast radius). This plugin provides the tools (real IaC validation, live CloudWatch metrics, latest AWS documentation).

---

## MCP Servers Included

| MCP Server | Source | What It Adds to Resiliency Reviews |
|---|---|---|
| **AWS IaC** | `awslabs.aws-iac-mcp-server` | CloudFormation validation via cfn-lint, compliance checking via cfn-guard, CDK best practices, deployment troubleshooting (30+ failure patterns) |
| **AWS Terraform** | `awslabs.terraform-mcp-server` | Terraform best practices, Checkov security scanning, Well-Architected guidance for Terraform configs |
| **AWS Knowledge** | `awslabs.aws-knowledge-mcp-server` | Up-to-date AWS documentation, Well-Architected materials, troubleshooting guides, regional availability |
| **Amazon CloudWatch** | `awslabs.cloudwatch-mcp-server` | Live metrics, alarm status/history, CloudWatch Logs Insights queries, trend detection, alarm recommendations |
| **CloudWatch App Signals** | `awslabs.cloudwatch-applicationsignals-mcp-server` | Service health, SLO compliance tracking, distributed tracing, code-level latency analysis |
| **AWS Documentation** | `awslabs.aws-documentation-mcp-server` | Direct AWS documentation page lookup and search across all services |

---

## How They Work Together

```
┌─────────────────────────────────────────────────────┐
│  aws-resiliency-skill (companion repo)              │
│  SKILL.md + references → resiliency expertise       │
│  "What to look for, failure modes, blast radius"    │
└───────────────────────┬─────────────────────────────┘
                        │ loaded as skill context
                        ▼
┌─────────────────────────────────────────────────────┐
│  Claude Code / Cursor / Kiro (host AI)              │
│  Combines skill knowledge + MCP tool results        │
└───────────────────────┬─────────────────────────────┘
                        │ calls tools from
                        ▼
┌─────────────────────────────────────────────────────┐
│  This plugin → 6 Official AWS MCP Servers           │
│                                                     │
│  aws-iac             → CFN lint, cfn-guard, CDK     │
│  aws-terraform       → Checkov, WA guidance         │
│  aws-knowledge       → WA docs, best practices      │
│  aws-cloudwatch      → Alarms, logs, metrics        │
│  aws-cloudwatch-signals → SLOs, tracing             │
│  aws-docs            → Service documentation        │
└─────────────────────────────────────────────────────┘
```

**Zero custom code. Zero API cost. Maintained by AWS.**

---

## Prerequisites

- Python 3.10+ with **uv** installed ([install uv](https://docs.astral.sh/uv/getting-started/installation/)) — the AWS MCP servers run via `uvx`
- AWS credentials configured (`aws configure` or environment variables) — required for CloudWatch and live account access

---

## Installation

### Claude Code

Add each server via CLI:

```bash
claude mcp add aws-iac -- uvx awslabs.aws-iac-mcp-server@latest
claude mcp add aws-terraform -- uvx awslabs.terraform-mcp-server@latest
claude mcp add aws-knowledge -- uvx awslabs.aws-knowledge-mcp-server@latest
claude mcp add aws-cloudwatch -- uvx awslabs.cloudwatch-mcp-server@latest
claude mcp add aws-cloudwatch-signals -- uvx awslabs.cloudwatch-applicationsignals-mcp-server@latest
claude mcp add aws-docs -- uvx awslabs.aws-documentation-mcp-server@latest
```

### Cursor / Windsurf / Other MCP Editors

Copy the contents of [`mcp.json`](mcp.json) into your editor's MCP server configuration file.

### Pair with the Skill (Recommended)

For the best resiliency reviews, also install the companion skill:

```bash
git clone https://github.com/nirmal84/aws-resiliency-skill.git
cp -r aws-resiliency-skill ~/.claude/skills/aws-resiliency-skill
```

---

## What Each Server Does in a Review

### AWS IaC → Validates your CloudFormation/CDK

When you share a CloudFormation template or CDK code:
- **cfn-lint** catches misconfigurations (missing `MultiAZ`, invalid properties, deprecated resources)
- **cfn-guard** enforces compliance rules ("every RDS must have `MultiAZ: true`")
- **CDK best practices** recommends L2/L3 constructs that encode resiliency by default
- **Deployment troubleshooting** diagnoses why stack rollbacks happened

### AWS Terraform → Validates your Terraform

When you share `.tf` files:
- **Checkov** scans for security and resiliency misconfigurations
- **Well-Architected guidance** maps Terraform resources to AWS best practices
- **terraform validate/plan** catches issues before deployment

### AWS Knowledge → Well-Architected expertise

When the skill needs to reference AWS best practices:
- Searches the full AWS documentation corpus
- Returns latest Well-Architected Reliability pillar content
- Checks regional availability of services in DR regions

### Amazon CloudWatch → Live observability data

When reviewing a running workload:
- Pulls actual metrics (CPU, latency, error rates)
- Checks if critical alarms exist and are correctly configured
- Runs CloudWatch Logs Insights queries against real logs
- Detects trends approaching thresholds

### CloudWatch Application Signals → SLO and tracing

When assessing application health:
- Checks if SLOs are being met (availability, latency targets)
- Traces requests across services to find failure points
- Identifies code-level latency bottlenecks

### AWS Documentation → Direct doc lookup

When the skill references a specific service behaviour:
- Fetches the actual AWS documentation page
- Confirms current failover timing, limits, and quotas

---

## File Structure

```
aws-resiliency-mcp/
├── mcp.json              # MCP server configuration (6 servers)
├── README.md             # This file
├── CONTRIBUTORS.md       # Contributors
├── LICENSE               # MIT-0 (MIT No Attribution)
└── .gitignore
```

---

## Related

- **[aws-resiliency-skill](https://github.com/nirmal84/aws-resiliency-skill)** — The companion skill that provides resiliency expertise, failure mode knowledge, and review templates
- **[awslabs/mcp](https://github.com/awslabs/mcp)** — Official AWS MCP servers (source of all 6 servers in this plugin)

---

## Author

**Nirmal Rajan** — [@nirmal84](https://github.com/nirmal84)
