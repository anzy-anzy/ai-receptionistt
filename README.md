# AI Voice Receptionist & Real-Time Order Dispatcher Pipeline

A production-grade, asynchronous automation pipeline designed to rescue missed calls for local brick-and-mortar businesses (e.g., cafes, restaurants, plumbers, and home service providers). This system deploys a low-latency AI voice agent capable of handling multi-turn inbound conversations, qualifying customer requests, parsing menu orders, calculating transaction totals live, and instantly dispatching a visually formatted, structured order receipt to the business owner via real-time webhooks upon call termination.

---

## 🚀 System Architecture & Data Flow

The solution leverages a modern, event-driven, decoupled tech stack optimized for performance, zero-maintenance scaling, and low latency:

[Inbound Call / Forwarded Line]
│
▼
[Vapi AI Voice Agent] ──(Reads/Writes Live)──> [Google Calendar API]
│
│ (On Call Disconnect: fires end-of-call-report)
▼
[Make.com Visual Webhook Engine]
│
├───> [Gmail/SMTP Module] ───> [Formatted HTML Email to Business Owner]
└───> [GHL / SMS API Module] ─> [Instant Text Notification to Owner's Cell]

1. **Voice Integration & NLU Layer (Vapi.ai):** Captures telephony traffic from real-time customer streams. It runs a custom persona system prompt to collect structural datasets (Name, Phone, Address, Order configuration) and handles real-time mathematical cost generation.

<img width="2920" height="1400" alt="image" src="https://github.com/user-attachments/assets/2ee57ded-b422-4867-9e15-d074a029065a" />

2. **Execution & Routing Layer (Make.com):** Acts as the visual automation orchestrator. It acts as an asynchronous web receiver, capturing data strings exactly when the call hangs up.

  <img width="2930" height="1514" alt="image" src="https://github.com/user-attachments/assets/615b8476-3cad-4318-9721-8c26d3f7336f" />

3. **Data Payload Extraction:** The system filters out standard system traffic to ingest only high-confidence `end-of-call-report` packets. It then extracts conversational tokens dynamically.
6. **Notification Relaying (Gmail/SMTP/GHL):** Structures incoming raw text strings into styled HTML elements (`white-space: pre-wrap` containers) to maintain readability inside the final communication targets.

<img width="2924" height="1460" alt="image" src="https://github.com/user-attachments/assets/d52d4bd3-1c8e-4a93-b917-3187d0c8d078" />

---

## 🛠️ Configuration & System Prompts

### 1. The Core AI Agent Prompt Configuration
This prompt structures the persona into a precise data-collection engine, ensuring specific operational guardrails for a localized business entity:

```text
# Persona & Role
You are "Maria", the friendly and highly professional AI office receptionist for "Citas Cafe" located in Hollister, California. Your primary goal is to answer customer menu FAQs, handle quick pickup orders, calculate live pricing, and schedule table reservations.

# Rules of Engagement
1. Greeting: Start with: "Thanks for calling Citas Cafe in Hollister! This is Maria. Are you looking to place a pickup order or book a table today?"
2. Information Gathering: You must collect exactly three pieces of information before finalizing a pickup order:
   - The customer's full name.
   - Their exact food/drink order.
   - The number of minutes until they want to arrive for pickup.
3. Pricing Rules:
   - Latte: Small \$4.50, Large \$5.50 | Cold Brew: \$5.00
   - Breakfast Burrito: \$8.50 | Avocado Toast: \$7.00
   - Dynamically calculate the total price live over the phone and state it clearly.
4. Finalizing: Inform the customer that their order is locked in, and that they will pay at the register when they arrive.

# System Safeguards
- If a customer asks where we are located, say: "We are right in the heart of Hollister! Stop on by anytime."
- Keep responses short, conversational, and energetic—like a busy barista.
- Live Call Transfer: If there is an extreme emergency, trigger the native forwarding system to patch in a human manager.
```

### 2. Make.com Payload Filter Specification
To optimize running costs and prevent empty alert generation during call initializations, a strict data filter is placed on the canvas pipeline:
- **Condition Parameter Group:** `type`
- **Text-Operator Filter Logic:** `Equal to`
- **Assigned Targeting Value:** `end-of-call-report`

---

## 📬 Visual Email Template Layout (HTML Source)

The pipeline dynamically reformats unstructured conversation text into a responsive HTML email layout container, injecting custom CSS accent borders directly into the communication node payload:

```html
<h3>☕ New Inbound Order Dispatched: Citas Cafe</h3>
<hr>
<b>Order Details & Customer Conversation:</b> 
<br><br>
<div style="background-color: #f9f9f9; padding: 15px; border-left: 4px solid #007bff; font-family: monospace; white-space: pre-wrap;">
  {{1.message.summary}}
  <br><br>
  {{1.message.transcript}}
</div>
<hr>
<p style="color: #666; font-size: 12px;">This transaction record was taken live by your automated AI Receptionist.</p>
```
<img width="2866" height="1468" alt="image" src="https://github.com/user-attachments/assets/b45de0e5-6e8d-49fe-a2db-2bba93c461df" />

---

## 📈 Scalable Agency Deployment Architecture

For scalable commercial deployments, this system converts from an isolated workflow into a premium agency business system via simple production adaptations:
- **Telephony Deployment:** Provision local numbers instantly directly inside Vapi dashboards (\$2/month) and route existing office phone traffic using Conditional Call Forwarding (`CCF`).
- **Data Capture Expansion:** Drop a **GoHighLevel (GHL)** module or a programmatic SMS API relay (Telnyx/SignalWire) directly onto the Make canvas to automatically update customer records and fire internal summary text alerts directly to a business owner's cell phone.
- **Enterprise Scheduling:** Connect Vapi's tool settings directly to the Google Calendar API provider slot, permitting the voice engine to read live availability matrices and schedule bookings automatically during the call.
