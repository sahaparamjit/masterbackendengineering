# Master Backend Engineering – Agent Context

## 1. Project Overview
This repository hosts the official website for **Master Backend Engineering**
(https://masterbackendengineering.com).

The website represents a **personal consulting and mentorship brand** focused on:
- Backend engineering
- System design
- Scalable distributed systems
- Career growth from Senior → Staff-level engineers

The site is intentionally simple, static, and fast.

Hosting:
- GitHub Pages
- Custom domain configured and working
- HTTPS enabled

---

## 2. Current Goals (IMPORTANT)
The primary goals of the website are:

1. Establish credibility and authority
2. Clearly explain consulting / mentorship offerings
3. Convert visitors into:
   - 1:1 consultation bookings
   - Direct messages / inquiries

This is **not** a blog-heavy or content-heavy site yet.

---

## 3. Target Audience
Primary:
- Working software engineers (3–10+ years)
- Backend / system design interview candidates
- Engineers aiming for Staff / Senior roles

Secondary (future):
- Frontend engineers (architecture, performance, systems-level frontend)
- Full-stack engineers

The brand should feel:
- Senior
- Calm
- Practical
- Experience-driven
- Non-salesy

---

## 4. Current Site Structure
The site is a static HTML/CSS website.

Core files:
- `index.html` – homepage
- `style.css` – global styling
- (Future) `services.html`, `about.html`

No backend services are currently used.

---

## 5. Hosting & Deployment Rules (CRITICAL)
- The `main` branch represents **production**
- Changes must never be pushed blindly to `main`
- All non-trivial changes should go through:
  - A PR
  - A preview branch (if applicable)
- GitHub Pages is the deployment mechanism

The agent must:
- Propose changes
- Generate diffs or PRs
- Never auto-deploy without human approval

---

## 6. Analytics & Tracking
Currently enabled:
- Google Analytics 4 (GA4)

Tracked interactions include:
- Page views
- CTA clicks (e.g., Book Consultation)
- Form submissions (or intent)

The agent should preserve analytics scripts and event tracking
unless explicitly asked to modify them.

---

## 7. Brand Evolution Strategy
Current positioning:
- Backend & System Design consulting

Planned evolution:
- Expand into frontend engineering **without rebranding**
- Frontend focus should be:
  - Architecture
  - Performance
  - Systems thinking
  - Not basic tutorials

Future expansion may include:
- Full-stack systems
- Multiple mentors
- Sub-sections or subdomains

Avoid:
- Diluting backend authority
- Generic “we do everything” messaging

---

## 8. Tone & Copy Guidelines
All content changes should follow these principles:

- Clear over clever
- Experience > buzzwords
- Tradeoffs > theory
- Short, confident sentences
- Avoid marketing fluff

Good:
> “I help engineers design scalable systems and grow into senior roles.”

Bad:
> “We provide cutting-edge, next-generation, industry-leading solutions.”

---

## 9. What the Agent SHOULD Help With
The agent is encouraged to:
- Rewrite copy for clarity and conversion
- Improve structure and information hierarchy
- Suggest better CTAs
- Add frontend consulting sections carefully
- Optimize layout while keeping simplicity

---

## 10. What the Agent MUST NOT Do
- Change DNS or domain configuration
- Add heavy frameworks without request
- Introduce backend services without approval
- Remove analytics or tracking silently
- Push directly to production

---

## 11. Success Definition
The website is successful if:
- Visitors clearly understand what is offered
- CTA clicks increase
- Visitors reach out for consultations
- The site feels trustworthy and senior

---

## 12. Agent Operating Principle
Intent → Proposal → Review → Deploy

Never:
Proposal → Auto-deploy