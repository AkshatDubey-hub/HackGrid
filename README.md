-------------- Project: FetchFund -------------
> **A GenAI Public Advisory Platform Connecting Citizens, Micro-Borrowers, and Marginalized Communities to Statutory Government Loans, Grants, and Welfare Subsidies.**

---

## 1. Problem Understanding

Governments at both Central and State levels allocate vast financial resources every year toward subsidized credit, affirmative action loans, and enterprise grants. Schemes like PM SVANidhi (street vendors), PM Vishwakarma (artisans/craftspeople), PMEGP (micro-enterprises), Stand-Up India (SC/ST and women entrepreneurs), Mudra (small businesses), and State Backward Classes / SC / ST Development Corporation credit facilities exist specifically to foster economic inclusion.

Despite substantial statutory funding, millions of intended beneficiaries remain financially excluded due to persistent bottlenecks:

* **Information Asymmetry & Regulatory Jargon:** Official guidelines, government gazettes, and ministry portals are dense, legalistic, and difficult for average citizens to interpret.
* **Lack of Awareness of Entitlements:** Most eligible individuals—street vendors, daily wage workers, small shop owners, artisans, and economically backward groups—are simply unaware of the specific subsidies, interest subventions, and affirmative credit quotas they are legally entitled to.
* **The "Bank Counter" Hurdle:** When under-informed citizens visit bank branches, they are frequently dismissed or turned away by bank personnel due to missing papers, minor documentation doubts, or an inability to name the exact statutory scheme.
* **Exploitation by Predatory Intermediaries:** Lacking guidance, citizens often turn to informal middlemen charging exorbitant commission cuts or resort to local loan sharks operating at 36%–60%+ APR.

---

## 2. Proposed Solution

**FetchFunds** is a zero-friction, conversational advisory engine powered by the Google Gemini API. It bridges the gap between citizens and institutional support by serving as an intelligent advisory layer.

Using a single natural language input (spoken vernacular or text), an individual can simply describe their background, location, social category, and requirement (e.g., *"I run a small tailoring shop in a semi-urban town, belong to an SC community, have no collateral, and need ₹50,000 for a commercial sewing machine"*).

The engine instantly processes this unstructured information and returns a clear, actionable 4-part dossier:
1. **Matched Government Scheme(s) & Grants:** Pinpoints the 1–2 most beneficial schemes based on user criteria (e.g., Mudra Shishu, PM Vishwakarma, NSFDC/NSTFDC, Stand-Up India).
2. **Subsidies & Net Borrowing Cost:** Explains available capital subsidies, interest subventions (e.g., prompt repayment rebates), and the net effective interest rate.
3. **Document Readiness Checklist:** A concise, practical checklist of 3–4 essential documents required by the bank/agency.
4. **Bank Counter Approach Script:** A clear, respectful 2-to-3 sentence script tailored to the citizen's profile and language that they can speak or show directly to the bank manager or loan officer to assert their eligibility under priority lending rules.

---

## 3. System Architecture

To adhere strictly to hackathon constraints (**single-prompt execution, no complex multi-agent frameworks**), **[Project Name]** uses a streamlined **In-Context Single-Prompt Architecture**.

```
[ Citizen Input ] (Vernacular Voice-to-Text / Plain Text Query)
       │
       ▼
[ Client Interface ] (Streamlit Web App / Mobile Web / WhatsApp Bot)
       │
       ▼
[ In-Context System Engine ]
   ├── System Role: Statutory Welfare & Priority Credit Advisory Underwriter
   ├── In-Context Knowledge Base: Consolidated Central & State Scheme Rules
   └── Query Injection: Raw User Context, Location & Social Demographics
       │
       ▼
[ Google Gemini API ] (gemini-1.5-flash)
       │ Single-Shot Inference (~600ms latency, deterministic structured output)
       ▼
[ Structured Advisory Dossier ]
   ├── 1. Matched Scheme(s) & Credit Limit
   ├── 2. Subsidies, Margin Money & Net APR
   ├── 3. Essential Document Checklist
   └── 4. Bank Counter Approach Script
```

### Single-Prompt Implementation

```python
import os
import google.generativeai as genai

genai.configure(api_key=os.environ.get("GEMINI_API_KEY"))

SYSTEM_PROMPT = """
You are an expert government credit and statutory welfare scheme advisor for Indian citizens, small business owners, artisans, street vendors, women entrepreneurs, and economically backward communities (SC/ST/OBC/Minorities).

SCHEME KNOWLEDGE BASE:
1. PM SVANidhi: Micro-credit for urban/semi-urban street vendors and hawkers. 1st tranche up to ₹10,000, 2nd tranche ₹20,000, 3rd tranche ₹50,000. 7% interest subsidy on digital/prompt repayment. No collateral.
2. PM Vishwakarma: Subsidized collateral-free credit for traditional artisans/craftspersons (18 trades). Tranche 1 up to ₹1,00,000 at 5% concessional interest; Tranche 2 up to ₹2,00,000. Includes ₹15,000 toolkit incentive grant.
3. Pradhan Mantri Mudra Yojana (PMMY): Micro-enterprise loans. Shishu (up to ₹50,000, no processing fee, no collateral), Kishor (₹50,000 to ₹5,00,000), Tarun (₹5,00,000 to ₹10,00,000).
4. PMEGP (Prime Minister Employment Generation Programme): Credit-linked capital subsidy for setting up micro-enterprises. Up to ₹50 Lakhs (manufacturing) / ₹20 Lakhs (services). Subsidies range from 15% to 35% (higher for SC/ST/OBC/Women/Rural).
5. Stand-Up India: Bank loans from ₹10 Lakh to ₹1 Crore for greenfield enterprises in manufacturing, services, or trading to at least one SC/ST and one woman borrower per bank branch.
6. NSFDC / NSTFDC / NBCFDC: Subsidized micro-credit and term loans specifically for SC, ST, and OBC communities below statutory income limits with interest rates between 4% and 6% p.a.

INSTRUCTIONS:
Evaluate the user profile. Recommend the top 1 to 2 most relevant schemes and format the output strictly as follows:

1. 🏛️ Matched Scheme & Funding Limit: Scheme name, implementing ministry/body, and maximum eligible amount.
2. 💰 Subsidies & Net Interest Rate: Applicable capital subsidy, interest subvention rate, and net out-of-pocket borrowing cost.
3. 📄 Document Readiness Checklist: 3 to 4 essential documents to take along (e.g., Aadhaar, caste certificate, trade/vending proof, bank passbook).
4. 🗣️ Bank Counter Approach Script: A respectful, firm 2-sentence statement the applicant can say directly to the bank branch manager to formally request the application under statutory priority guidelines.
"""

def get_scheme_advisory(user_input: str) -> str:
    model = genai.GenerativeModel(
        model_name="gemini-1.5-flash",
        system_instruction=SYSTEM_PROMPT
    )
    response = model.generate_content(
        f"CITIZEN PROFILE AND NEED:\n{user_input}"
    )
    return response.text
```

---

## 4. Resource Utilization

The solution is intentionally designed for minimal infrastructure footprint and maximum cost efficiency:

| Resource Category | Implementation Details | Efficiency & Cost Profile |
| :--- | :--- | :--- |
| **GenAI Inference** | Google Gemini API | Single-shot prompt. Costs < ₹0.05 per inquiry; runs well within hackathon free tiers. |
| **Compute & Hosting** | Serverless Web Application (Streamlit Cloud / Vercel / Render) | Zero local server maintenance, auto-scaling, ₹0 deployment during development and evaluation. |
| **Storage & Privacy** | Ephemeral Processing (No PII Database) | Compliant with DPDP Act 2023. User data is processed dynamically in memory and never persisted, removing data breach risks. |
| **Operational Overhead** | In-Context Dynamic Updates | Scheme rules, subsidy slabs, and interest rates are maintained directly inside the prompt context—eliminating the need for complex model fine-tuning or vector databases. |

---

## 5. Development Plan

### Phase 1: Prototype & Evaluation 
- [x] Consolidate primary Central & State welfare credit schemes into a structured context base.
- [x] Implement and optimize the single-prompt system instructions on Gemini API.
- [x] Build an intuitive, one-box input UI (Streamlit) accepting plain language queries.
- [x] Benchmark recommendations against realistic profiles (street vendors, small shopkeepers, SC/ST entrepreneurs, artisans).

### Phase 2: Accessibility & Grassroots Reach 
- Integrate vernacular voice-to-text (using open speech APIs) to support unlettered or regional-language speakers.
- Deploy a lightweight WhatsApp bot interface for direct distribution without requiring app downloads.
- Partner with Common Service Centers (CSCs) and local community kiosks to test assisted walk-in workflows.

### Phase 3: Institutional Scale & Integration
- **Kiosk & Field Partner Tool:** Provide the system to Village Level Entrepreneurs (VLEs) and banking correspondents (*Bank Mitras*) as a rapid advisory reference.
- **Priority Sector Lending (PSL) Enablement:** Offer banks and microfinance institutions pre-informed, document-ready applicants, reducing turnaround times and rejection rates.
- **Open NGO / Welfare API:** Provide API access for micro-lending portals, non-profits, and livelihood missions to embed automated scheme matching into their operations.

###Profitability:
-- Bank PSL Sourcing / Origination Fees:
1) Commercial and Regional Rural Banks (RRBs) are mandated by the Reserve Bank of India (RBI) to allocate 40% to 75% of their lending to Priority Sectors (MSMEs, street vendors, weaker sections, agriculture).- Banks routinely struggle to source viable, document-ready applicants from marginalized communities without heavy field-agent operational expenses.
2) The platform monetizes by partnering with banks and Business Correspondents (BCs), earning an origination/referral fee (0.5%–1% of disbursed loan value) for delivering pre-qualified applicants equipped with complete checklists.
--B2B API Licensing for FinTech & AgTech:
1) FinTech apps (e.g., digital bookkeeping, merchant QR platforms, supply-chain apps) want to offer credit and grant discovery to keep users engaged.
2) JanSetu licenses its single-prompt matching engine via a developer API, charging per-query API access fees.
CSR & Livelihood Mission Grants:
3) In the early stages, non-dilutive corporate social responsibility (CSR) grants under Schedule VII of the Companies Act fund inference infrastructure, specifically under financial inclusion and digital literacy mandates.
