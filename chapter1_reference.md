# Chapter 1: Multi-Agent Marketing System — Reference Guide

This reference guide contains all the prompts, schemas, and code you need to complete the exercises in Chapter 1.

---

## Workflow Summary

Start here! This diagram shows how data flows through your AI marketing department:

```
Manual Trigger
    ↓
Enter business details (Set node with 12 fields)
    ↓
CMO Agent → IF validation → [error handling]
    ↓
Marketing Operations Manager → IF validation → [error handling]
    ↓ (splits into 3 parallel branches)
┌───────────────────┬───────────────────┬───────────────────┐
Brand Identity      Content Marketing   Performance Marketing
Agent               Agent               Agent
    ↓                   ↓                   ↓
IF validation       IF validation       IF validation
    ↓                   ↓                   ↓
└───────────────────┴───────────────────┴───────────────────┘
    ↓ (merge all 3)
Merge Strategy Sections (3 inputs)
    ↓
Assembler (Code node)
    ↓
Edit Fields (Set node)
    ↓
CMO Summary Agent
    ↓
Final Executive Brief
```

**The flow in simple terms:**
1. Business data enters through a Set node
2. CMO Agent creates 3 strategic tasks
3. Operations Manager refines tasks into detailed assignments
4. 3 Specialist Agents work in parallel to generate strategies
5. Assembler combines all outputs into one document
6. Summary Agent creates an executive brief

---

## Business Intake Fields

Copy and paste this JSON into the "Enter business details" Set node (switch to **JSON mode** first):

```json
{
  "Business Name & Description": "FlowPilot — a B2B sales automation platform that automates lead qualification, outreach sequencing, and CRM updates for small sales teams.",
  "Target Audience / Persona": "Sales managers in small B2B companies (5–30 reps), overwhelmed by manual outreach, poor pipeline hygiene, and inconsistent follow-up.",
  "Key Competitors": "Apollo, Reply.io, SalesLoft (SMB tier).",
  "Marketing Budget": "Medium ($8k–$15k/month).",
  "Current Assets": "Website, 20 blog posts, one outdated case study, small LinkedIn page.",
  "Main Goal": "Increase demo bookings by 40% within 90 days.",
  "What is your main challenge right now?": "Traffic is okay, but conversion from page visits → demo calls is weak.",
  "What have you already tried in the last 3–6 months?": "Google Search ads, a few webinars, inconsistent content posting.",
  "What makes your product or service different?": "Fastest setup in the industry (1 hour), automatic CRM cleanup, and no engineering required.",
  "What objections do your customers usually have?": "Too many automation tools already, Will this overload prospects? Is this safe for my CRM data?",
  "Where does most of your traffic / leads come from today?": "Organic search (SEO), direct traffic, and LinkedIn posts.",
  "If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?": "Improve demo conversion rate without increasing ad spend."
}
```

---

## CMO Agent

### Define Prompt (Text)

```
User input:

Business Name & Description: {{ $json['Business Name & Description'] }}
Target Audience / Persona: {{ $json['Target Audience / Persona'] }}
Key Competitors: {{ $json['Key Competitors'] }}
Marketing Budget: {{ $json['Marketing Budget'] }}
Current Assets: {{ $json['Current Assets'] }}
Main Goal: {{ $json['Main Goal'] }}
What is your main challenge right now?: {{ $json['What is your main challenge right now?'] }}
What have you already tried in the last 3–6 months?: {{ $json['What have you already tried in the last 3–6 months?'] }}
What makes your product or service different?: {{ $json['What makes your product or service different?'] }}
What objections do your customers usually have?: {{ $json['What objections do your customers usually have?'] }}
Where does most of your traffic / leads come from today?: {{ $json['Where does most of your traffic / leads come from today?'] }}
If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?: {{ $json['If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?'] }}


Based on the user's input, generate a task for each of the below specialized agents:

1. brand_identity_agent
2. content_strategy_agent
3. paid_ads_agent


Respond ONLY with valid JSON, no commentary, no markdown.
```

### System Message

```
You are a world-class Chief Marketing Officer (CMO) with expertise in digital strategy, branding, and growth hacking. Your goal is to take raw business information and create tasks for specialized agents.
```

### JSON Schema (for Structured Output Parser)

```json
{
  "brand_identity_task": "...",
  "content_strategy_task": "...",
  "paid_ads_task": "..."
}
```

---

## Marketing Operations Manager

### Define Prompt (Text)

```
Take the CMO's tasks for specialized agents and refine and clarify tasks.
But do not ignore the CMO's actual content.

CMO's assigned tasks for specialized agents:

brand_identity_agent_task: {{ $json.output.brand_identity_task }}

content_strategy_agent_task: {{ $json.output.content_strategy_task }}

paid_ads_agent_task: {{ $json.output.paid_ads_task }}

Keep tasks high-level and strategical. Don't request development of full assets like complete blog posts or documents. 
Respond ONLY with valid JSON, no commentary, no markdown.
```

### System Message

```
You are a Marketing Operations Director. Your goal is to receive the tasks for the specialized agents from the CMO and break it down into distinct, isolated instructions for specialist teams.
```

### JSON Schema (for Structured Output Parser)

```json
{
  "brand_identity_detailed_task": "...",
  "content_strategy_detailed_task": "...",
  "paid_ads_detailed_task": "..."
}
```

---

## Brand Identity Agent

### Define Prompt (Text)

```
Based strictly on the assignment, generate the a simple Brand Identity Strategy document (800 words max).

Business context form:

Business Name & Description: {{ $('Enter business details').item.json['Business Name & Description'] }}

Target Audience / Persona: {{ $('Enter business details').item.json['Target Audience / Persona'] }}

Key Competitors: {{ $('Enter business details').item.json['Key Competitors'] }}

Marketing Budget: {{ $('Enter business details').item.json['Marketing Budget'] }}

Current Assets: {{ $('Enter business details').item.json['Current Assets'] }}

Main Goal: {{ $('Enter business details').item.json['Main Goal'] }}

What is your main challenge right now?: {{ $('Enter business details').item.json['What is your main challenge right now?'] }}

What have you already tried in the last 3–6 months?: {{ $('Enter business details').item.json['What have you already tried in the last 3–6 months?'] }}

What makes your product or service different?: {{ $('Enter business details').item.json['What makes your product or service different?'] }}

What objections do your customers usually have?: {{ $('Enter business details').item.json['What objections do your customers usually have?'] }}

Where does most of your traffic / leads come from today?: {{ $('Enter business details').item.json['Where does most of your traffic / leads come from today?'] }}

If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?: {{ $('Enter business details').item.json['If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?'] }}

Here is your assignment from the Marketing Operations Manager:

"{{ $json.output.brand_identity_detailed_task }}"
```

### System Message

```
You are a World-Class Brand Strategist and Creative Director with 20+ years of experience building iconic brands.

Your goal is to define the "Soul" of the business. You do not care about SEO or algorithms; you care about human emotion, psychology, and storytelling.

Your output must cover:
1. Brand Archetype & Personality (e.g., The Hero, The Outlaw).
2. Core Value Proposition (The "Why Us").
3. Tone of Voice Guidelines (vocabulary, sentence structure, emotional tone).
4. Visual Direction Brief (color psychology, typography mood, imagery style).

Format: Professional Markdown. clear headers, bullet points, and bold text for emphasis. No fluff.
```

---

## Content Marketing Agent

### Define Prompt (Text)

```
Based strictly on the assignment, generate the a simple Brand Identity Strategy document (800 words max).

Business context form:

Business Name & Description: {{ $('Enter business details').item.json['Business Name & Description'] }}

Target Audience / Persona: {{ $('Enter business details').item.json['Target Audience / Persona'] }}

Key Competitors: {{ $('Enter business details').item.json['Key Competitors'] }}

Marketing Budget: {{ $('Enter business details').item.json['Marketing Budget'] }}

Current Assets: {{ $('Enter business details').item.json['Current Assets'] }}

Main Goal: {{ $('Enter business details').item.json['Main Goal'] }}

What is your main challenge right now?: {{ $('Enter business details').item.json['What is your main challenge right now?'] }}

What have you already tried in the last 3–6 months?: {{ $('Enter business details').item.json['What have you already tried in the last 3–6 months?'] }}

What makes your product or service different?: {{ $('Enter business details').item.json['What makes your product or service different?'] }}

What objections do your customers usually have?: {{ $('Enter business details').item.json['What objections do your customers usually have?'] }}

Where does most of your traffic / leads come from today?: {{ $('Enter business details').item.json['Where does most of your traffic / leads come from today?'] }}

If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?: {{ $('Enter business details').item.json['If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?'] }}

Here is your assignment from the Marketing Operations Manager:

"{{ $json.output.content_strategy_detailed_task }}"
```

### System Message

```
You are an Expert Editor-in-Chief and Content Strategist. You specialize in building high-authority content engines that drive traffic and establish thought leadership.

Your output must cover:
1. Core Content Pillars (3-5 main topics the brand will own).
2. Funnel Mapping (What content works for Top, Middle, and Bottom of the funnel).
3. 10 High-Impact Blog/Article Titles with brief outlines.
4. Video Content Strategy (YouTube/Webinar concepts).

Format: Strategic Content Calendar style. Markdown tables where appropriate.
```

---

## Performance Marketing Agent

### Define Prompt (Text)

```
Based strictly on the assignment, generate the a simple Brand Identity Strategy document (800 words max).

Business context form:

Business Name & Description: {{ $('Enter business details').item.json['Business Name & Description'] }}

Target Audience / Persona: {{ $('Enter business details').item.json['Target Audience / Persona'] }}

Key Competitors: {{ $('Enter business details').item.json['Key Competitors'] }}

Marketing Budget: {{ $('Enter business details').item.json['Marketing Budget'] }}

Current Assets: {{ $('Enter business details').item.json['Current Assets'] }}

Main Goal: {{ $('Enter business details').item.json['Main Goal'] }}

What is your main challenge right now?: {{ $('Enter business details').item.json['What is your main challenge right now?'] }}

What have you already tried in the last 3–6 months?: {{ $('Enter business details').item.json['What have you already tried in the last 3–6 months?'] }}

What makes your product or service different?: {{ $('Enter business details').item.json['What makes your product or service different?'] }}

What objections do your customers usually have?: {{ $('Enter business details').item.json['What objections do your customers usually have?'] }}

Where does most of your traffic / leads come from today?: {{ $('Enter business details').item.json['Where does most of your traffic / leads come from today?'] }}

If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?: {{ $('Enter business details').item.json['If you could wave a magic wand and fix 1 thing in your business instantly, what would it be?'] }}

Here is your assignment from the Marketing Operations Manager:

"{{ $json.output.paid_ads_detailed_task }}"
```

### System Message

```
You are a Senior Performance Marketer and Media Buyer. You care about one thing: ROAS (Return on Ad Spend). You are analytical, data-driven, and ruthless about budget efficiency.

Your output must cover:
1. Channel Strategy (Meta vs. Google vs. LinkedIn vs. TikTok Ads).
2. Audience Targeting (Demographics, Interests, Lookalikes).
3. Ad Creative Briefs (3 distinct angles for ad copy/visuals).
4. Budget Allocation (How to split the budget for testing vs. scaling).

Format: Professional Strategy Memo. Use data terms (CPC, CTR, CPA) correctly.
```

---

## CMO Summary Agent

### Define Prompt (Text)

```
You are the CMO Master Summary Agent.

Your job is to take long strategy documents created by upstream agents (Brand Identity, Content Strategy, Paid Ads) and compress them into a single, clear executive brief.

Rules:
- 400–600 words max
- Prioritize clarity, not detail
- Highlight contradictions or overlaps between agents
- Extract only the most actionable steps
- Preserve the business context constraints from the form
- No generic marketing advice, no clichés
- Use bullet points, sections, and concrete examples
- Include a final "90-Day Action Plan"

This is your input:
{{ $json.text }}
```

### System Message

```
You are a world-class Chief Marketing Officer (CMO) with expertise in digital strategy, branding, and growth hacking. Your goal is to take raw business information and create tasks for specialized agents.
```

---

## Assembler JS Code

Copy this JavaScript code into the Code node:

```javascript
const input = $input.all();

function decode(str) {
  try {
    return JSON.parse(`"${str}"`);
  } catch (e) {
    return str;
  }
}

function fixNewLines(str) {
  if (str === undefined || str === null) return '';
  return decode(str).replace(/\\n/g, `
  `);
}

function pickContent(item) {
  // prefer agent result
  if (item.json.output) return fixNewLines(item.json.output);
  // fallback to error message
  if (item.json.Error) return `ERROR: ${item.json.Error}`;
  // last fallback
  return 'No content generated for this section.';
}

const sections = [
  { title: "Brand Identity Strategy", content: pickContent(input[0]) },
  { title: "Content Marketing Strategy", content: pickContent(input[1]) },
  { title: "Paid Ads and Performance Strategy", content: pickContent(input[2]) },
];

let text = "";

sections.forEach(section => {
  text += `# ${section.title}
${section.content}

`;
});

return {
  raw_output: sections,
  text: text,
};
```

---

## Quick Reference: Validation Conditions

### CMO Agent Validation
Check these three fields exist (AND logic):
- `{{ $json.output.brand_identity_task }}`
- `{{ $json.output.content_strategy_task }}`
- `{{ $json.output.paid_ads_task }}`

### Marketing Operations Manager Validation
Check these three fields exist (AND logic):
- `{{ $json.output.brand_identity_detailed_task }}`
- `{{ $json.output.content_strategy_detailed_task }}`
- `{{ $json.output.paid_ads_detailed_task }}`

### Specialist Agent Validation
Check this field exists:
- `{{ $json.output }}`

