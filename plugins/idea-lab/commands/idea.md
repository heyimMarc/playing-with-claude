# Idea Lab

Design research phase for exploring ideas before technical implementation. Combines product strategy, user research, competitive analysis, and technical feasibility assessment.

## Usage

```
/idea "Your idea description"
/idea --quick "Fast concept sketch"
/idea --deep "Comprehensive research"
/idea --competitive "Include competitive analysis"
```

## Flags

| Flag | Description |
|------|-------------|
| `--quick` | Fast concept sketch (5 min) |
| `--deep` | Comprehensive research with web analysis |
| `--competitive` | Include competitive/market analysis |

---

## Internal Implementation

When invoked with: $ARGUMENTS

### Step 1: Create Idea Document

```
Create: cc-plans/ideas/<idea-slug>.md

# Idea: <Name>
Created: <timestamp>
Status: Researching
Depth: <quick|standard|deep>

## Original Concept
$ARGUMENTS

## Research Log
[Will be populated during research]
```

### Step 2: Update Todo List

```
Use TodoWrite:
1. Analyze the idea concept
2. Research user needs
3. Assess technical feasibility
4. Competitive analysis (if --competitive)
5. Synthesize findings
6. Make recommendation
```

### Step 3: Product Analysis

```
Launch product-visionary agent:

Model: claude-opus-4.5
Context: isolated

Prompt:
"Analyze this product idea: $ARGUMENTS

Evaluate:
1. Problem being solved
2. Target users and their needs
3. Value proposition
4. Market opportunity
5. Success metrics

Use WebSearch to research:
- Similar products/solutions
- Market trends
- User complaints about current solutions

Return:
- Problem statement (validated or refined)
- Target user segments
- Value proposition canvas
- Key risks and opportunities
- Initial success metrics"
```

### Step 4: User Experience Research

```
Launch ux-researcher agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Research user experience for: $ARGUMENTS

Analyze:
1. User workflows this would affect
2. Pain points it addresses
3. Interaction patterns needed
4. Accessibility requirements

Use WebSearch if --deep to research:
- UX patterns for similar features
- User expectations
- Common usability issues

Return:
- User journey map
- Key user flows
- Pain points addressed
- Interaction requirements
- Accessibility considerations"
```

### Step 5: Competitive Analysis (if --competitive or --deep)

```
Use WebSearch to research:
- Direct competitors
- Alternative solutions
- Market positioning
- Pricing models

Compile:
| Competitor | Approach | Strengths | Weaknesses | Price |
|------------|----------|-----------|------------|-------|
```

### Step 6: Technical Feasibility

```
Launch architect agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Assess technical feasibility for: $ARGUMENTS

Evaluate:
1. Integration with existing system
2. Technical complexity
3. Dependencies and constraints
4. Rough effort estimate
5. Technical risks

Use Task tool with 'Explore' to understand current codebase.

Return:
- Feasibility assessment (Easy/Medium/Hard/Very Hard)
- Key technical considerations
- Integration points
- Estimated complexity
- Technical risks"
```

### Step 7: Synthesize Findings

```
Combine all research into cohesive document:

## Problem Statement
[Refined problem statement]

## User Stories
As a [user], I want [capability], so that [benefit]
...

## Success Metrics
| Metric | Target | How to Measure |
|--------|--------|----------------|
| [KPI] | [Goal] | [Method] |

## Concept Design
### User Flows
[Primary and alternative flows]

### Key Interactions
[How users will interact]

## Technical Feasibility
- Complexity: [Easy/Medium/Hard]
- Effort: [T-shirt size]
- Risks: [Key technical risks]

## Competitive Landscape (if researched)
[Summary of competitors]

## Open Questions
- [ ] Question needing more research
- [ ] Question for stakeholders

## Recommendation
[ ] Ready for /feature - Proceed to implementation
[ ] Needs more research - Specific areas
[ ] Pivot - Consider alternative approach
[ ] Pause - Significant concerns
```

### Step 8: Summary Output

```
💡 Idea Research Complete: <name>

📋 Problem: [Validated/Refined/Unclear]
👥 Users: [Segments identified]
💰 Value: [High/Medium/Low potential]
🔧 Feasibility: [Easy/Medium/Hard]

📊 Key Findings:
- [Finding 1]
- [Finding 2]
- [Finding 3]

❓ Open Questions:
- [Question 1]
- [Question 2]

📌 Recommendation: [Ready|Research|Pivot|Pause]

📄 Full research: cc-plans/ideas/<slug>.md

🚀 Next: Use /feature "<idea>" to implement (if ready)
```

---

## Output Handoff

The idea document is designed to feed directly into `/feature`:

```
/feature "Implement <idea> as researched in cc-plans/ideas/<slug>.md"
```

The feature orchestrator will use the research as input for requirements and design.

---

## Quick Mode (--quick)

Skip web research, focus on:
1. Quick problem analysis
2. Basic user flow sketch
3. Technical feasibility check
4. Go/No-Go recommendation

Time: ~5 minutes

---

## Deep Mode (--deep)

Full research including:
1. Comprehensive web research
2. Competitive analysis
3. Market sizing
4. Detailed user research
5. Technical architecture sketch

Time: ~30 minutes
