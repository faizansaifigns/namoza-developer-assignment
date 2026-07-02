# Task 3 – Integration Design

## End-to-End Architecture

When a user submits the consultation form, the frontend sends the form data (Name, Phone, and Clinic Preference) to a backend API endpoint using an HTTPS POST request.

The backend acts as the central integration layer. It first validates and sanitizes the submitted data before checking whether a contact with the same phone number already exists in HubSpot. Since HubSpot performs duplicate detection primarily using email addresses, I would implement a custom lookup based on the phone number. If a matching contact is found, the existing record is updated. Otherwise, a new contact is created with the following properties:

- Name
- Phone Number
- Clinic Preference
- Source = "Google Ads - Consultation Landing Page"
- Lead Status = "New Enquiry"

After successfully updating HubSpot, the backend sends a WhatsApp confirmation message using the Karix WhatsApp Business API. Finally, it triggers the `consultation_form_submitted` conversion event so that Google Ads can optimize campaigns toward qualified leads.

I would use the **HubSpot CRM API** instead of the embedded HubSpot Form because it provides greater flexibility for custom validation, phone-based duplicate checking, and future integrations.

---

## Biggest Failure Point & Fallback

The biggest failure point is contact synchronization with HubSpot. Since HubSpot does not deduplicate contacts by phone number by default, duplicate records may be created if multiple submissions use the same phone number.

To prevent this, the backend should first search for an existing contact using the phone number before deciding whether to create or update the record. If HubSpot is temporarily unavailable, the submission should be stored in a retry queue or database so that synchronization can occur automatically once the service is restored.

---

## WhatsApp SLA Monitoring

The WhatsApp confirmation message must be delivered within two minutes. Possible delays include API failures, network latency, rate limiting, or temporary outages in the WhatsApp Business API.

To maintain the SLA, I would implement request logging, response status monitoring, retry logic with exponential backoff, and alerting for failed message deliveries. A monitoring dashboard would track message success rates, API response times, and pending retries so that operational issues can be detected and resolved quickly.