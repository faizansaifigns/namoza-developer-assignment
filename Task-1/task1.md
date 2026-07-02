# Task 1 – GTM Event Schema

## Objective

The objective of this implementation is to establish a comprehensive Google Tag Manager (GTM) and Google Analytics 4 (GA4) event tracking strategy for OrthoNow's website. The tracking setup will measure user interactions, identify conversion opportunities, analyze funnel drop-offs, and provide actionable insights for performance marketing campaigns.

---

# 1. GTM Event Schema

| Event Name | Trigger Type | Key Parameters | GA4 Report / Audience |
|------------|--------------|----------------|-----------------------|
| page_view | Page View | page_name, page_url, page_type | Pages & Screens Report |
| consultation_booking_started | Custom Event | clinic_location, specialty, source | Booking Funnel |
| booking_step_complete | Custom Event | step_number, step_name, clinic_location | Funnel Exploration |
| booking_completed | Custom Event | booking_id, clinic_location, specialty | Conversions |
| call_now_click | Click Trigger | page_name, clinic_location, button_location | CTA Performance |
| whatsapp_click | Click Trigger | page_name, clinic_location, device_type | WhatsApp Audience |
| patient_guide_form_submit | Form Submission | guide_name, page_name, lead_source | Lead Generation |
| patient_guide_download | Link Click | guide_name, file_type, page_name | Downloads Report |
| clinic_page_view | Page View | clinic_name, city, page_url | Clinic Performance Report |
| blog_scroll_50 | Scroll Depth | article_title, category, scroll_percentage | Content Engagement |
| blog_scroll_90 | Scroll Depth | article_title, category, scroll_percentage | Engaged Readers Audience |
| form_validation_error | JavaScript Event | field_name, error_type, page_name | UX Analysis |
| booking_abandoned | Custom Event | last_completed_step, clinic_location, session_id | Remarketing Audience |
| consultation_form_submitted | Form Submission | landing_page, campaign_source, form_type | Google Ads Conversion |
| session_start | Automatic GA4 Event | source, medium, device_category | Traffic Acquisition |

---

# 2. Booking Funnel Tracking

The appointment booking process consists of three steps:

1. Select Clinic Location & Specialty
2. Enter Patient Details
3. Confirm Booking

Each completed step pushes a custom event into the dataLayer, allowing GTM to trigger GA4 events and measure funnel progression.

---

## Step 1 – Clinic & Specialty Selection

### Trigger

Custom Event Trigger

### dataLayer Push

```javascript
window.dataLayer = window.dataLayer || [];

window.dataLayer.push({
  event: "booking_step_complete",
  step_number: 1,
  step_name: "location_specialty_selected",
  clinic_location: "Bengaluru",
  specialty: "Knee Pain",
  booking_flow: "consultation_booking"
});
```

---

## Step 2 – Patient Details

### Trigger

Custom Event Trigger

### dataLayer Push

```javascript
window.dataLayer.push({
  event: "booking_step_complete",
  step_number: 2,
  step_name: "patient_details_entered",
  clinic_location: "Bengaluru",
  preferred_date: "2026-07-05",
  booking_flow: "consultation_booking"
});
```

> **Note:** Personally Identifiable Information (PII) such as the patient's name and phone number is **not** sent to GA4.

---

## Step 3 – Booking Confirmation

### Trigger

Custom Event Trigger

### dataLayer Push

```javascript
window.dataLayer.push({
  event: "booking_completed",
  step_number: 3,
  step_name: "booking_confirmed",
  clinic_location: "Bengaluru",
  specialty: "Knee Pain",
  booking_status: "success"
});
```

---

# 3. GTM Trigger Configuration

| Booking Step | Frontend Action | GTM Trigger | GA4 Event |
|--------------|----------------|-------------|------------|
| Step 1 | User selects clinic and specialty, then clicks **Next** | Custom Event Trigger (`booking_step_complete`) | booking_step_complete |
| Step 2 | User enters patient details and clicks **Next** | Custom Event Trigger (`booking_step_complete`) | booking_step_complete |
| Step 3 | User confirms booking | Custom Event Trigger (`booking_completed`) | booking_completed |

---

# 4. GA4 Funnel Exploration

The following events will be used to build a Funnel Exploration report in GA4.

| Funnel Step | Event |
|-------------|-------|
| Booking Started | consultation_booking_started |
| Step 1 Completed | booking_step_complete (step_number = 1) |
| Step 2 Completed | booking_step_complete (step_number = 2) |
| Booking Completed | booking_completed |

This funnel enables the marketing team to identify where users abandon the booking process and optimize the user experience to improve conversion rates.

---

# 5. Google Ads Conversion

The primary conversion imported into Google Ads would be:

**consultation_form_submitted**

### Why?

The consultation form submission represents a qualified lead because the user has completed the primary goal of the landing page by requesting a consultation. This provides a stronger optimization signal than intermediate actions such as clicking the "Call Now" button or opening WhatsApp.

Events like `call_now_click`, `whatsapp_click`, and `booking_step_complete` are valuable as **micro-conversions** for measuring user engagement and identifying drop-offs, but they do not confirm that a user has successfully submitted an enquiry.

Using `consultation_form_submitted` as the primary conversion allows Google Ads Smart Bidding strategies, such as **Maximize Conversions** or **Target CPA**, to optimize campaigns toward users who are more likely to become qualified leads.

---

# Implementation Notes

- Google Tag Manager listens for custom `dataLayer.push()` events generated by the frontend application.
- The frontend developer is responsible for implementing the `dataLayer.push()` calls.
- GTM captures these custom events and forwards them to Google Analytics 4.
- GA4 stores the events for reporting, audience creation, and conversion tracking.
- Google Ads imports the primary conversion event from GA4 to optimize paid campaigns.