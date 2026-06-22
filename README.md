# HubSpot Event & Identify Tag — GTM Custom Template

A Google Tag Manager custom tag template for firing HubSpot tracking calls
directly from your GTM web container. Supports contact identification, page
view tracking, and custom behavioral events — all configurable through a
clean GTM UI without writing any code.

---

## Overview

HubSpot's tracking API lets you identify website visitors and track their
behavior across your site. This template wraps those calls into a GTM-native
experience, so your team can configure and deploy HubSpot tracking through
GTM triggers and variables — the same way you manage every other tag.

The template supports three core tracking patterns:

- **Identify a contact** — pass an email address and optional properties
  to HubSpot CRM
- **Track a page view** — fire a HubSpot page view against the current or
  a custom path
- **Track a custom behavioral event** — fire a named HubSpot event with
  optional event properties

These can be used independently or combined — for example, firing an
identify call alongside a custom event on a form submission.

---

## Prerequisites

This template does **not** load the HubSpot tracking script. The HubSpot
tracking code (`hs-script-loader`) must already be present on your website
before this tag fires — either embedded directly in your site's HTML or
loaded as a separate GTM tag that fires earlier in the tag sequence.

If HubSpot's tracking script has not loaded by the time this tag fires,
the tracking calls will not work.

---

## Installation

### Option 1 — Import from file

1. Download the `.tpl` file from this repository.
2. Open your **Google Tag Manager** container.
3. Navigate to **Templates** in the left-hand menu.
4. Under **Tag Templates**, click **New**.
5. Click the **More Actions** menu (⋮) in the top-right corner.
6. Select **Import** and choose the downloaded `.tpl` file.
7. Review the permissions and click **Save**.

### Option 2 — Community Template Gallery

If this template has been published to the GTM Community Template Gallery,
you can search for it directly inside GTM under **Templates → Search Gallery**.

---

## Creating a Tag

Once the template is installed:

1. Go to **Tags → New** in your GTM container.
2. Click **Tag Configuration** and select **HubSpot Event & Identify Tag**
   from the **Custom** section.
3. Configure the tag using the sections below.
4. Set your trigger.
5. Save and publish.

---

## Configuration

### Track Call Type

The first thing to configure is what kind of HubSpot call you want to make.
Use the **Select the HubSpot Track Call Type** dropdown to choose between:

| Option | What it does |
|---|---|
| **Identify + Event** | Fires a HubSpot identify call, with the option to also send a page view or custom event |
| **Event Only** | Fires a page view or custom event without any identify call |

---

### Identify + Event Mode

When **Identify + Event** is selected, the following fields appear.

#### Contact Identity Fields

| Field | Required | Description |
|---|---|---|
| **Email Address** | ✅ Yes | The visitor's email address. This is the primary identifier HubSpot uses to match or create a contact record. |
| **First Name** | Optional | The visitor's first name. |
| **Last Name** | Optional | The visitor's last name. |

These fields accept GTM variables — in most cases you'll be pulling these
values from a form submission event, a data layer push, or a cookie.

#### Custom Contact Properties

Enable **"Enable if you want to pass custom properties"** if you need to
send additional fields beyond email, first name, and last name.

When enabled, a table appears where you can add rows for each custom
property:

| Column | Description |
|---|---|
| **Custom Field/Property Name** | The internal HubSpot property name, exactly as it appears in HubSpot (e.g. `account_type`, `subscription_tier`) |
| **Value** | The value to send for that property — accepts GTM variables |

> ⚠️ **Important:** Use the internal HubSpot property name, not the display
> label shown in the HubSpot UI. You can find internal names in your
> HubSpot account under **Settings → Properties**.

#### Adding Event Tracking to an Identify Call

HubSpot requires a separate `trackPageView` or `trackCustomBehavioralEvent`
call to associate an identified contact with an event. The identify call
alone does not record a tracked activity.

Enable **"Also send event tracking calls"** to include an event alongside
the identify. When enabled, the **HubSpot Event Tracking Config** section
appears below. The event fires **before** the identify call is sent.

---

### Event Tracking Config

This section appears when either:

- **Event Only** is selected as the track call type, or
- **Also send event tracking calls** is enabled within Identify + Event mode

#### Select Event Type

Use the **Select Event Type** dropdown to choose:

| Option | Description |
|---|---|
| **Page View** | Fires a HubSpot `trackPageView` call |
| **Custom Event** | Fires a HubSpot `trackCustomBehavioralEvent` call |

---

#### Page View Settings

When **Page View** is selected, use **Set the Page Path** to control
which path HubSpot records for this page view:

| Option | Description |
|---|---|
| **Automatically Set It** | The template reads the current page path from the browser automatically |
| **Manually Set It** | You supply the page path yourself using the **Enter Page Path Variable** field |

Use the manual option when you need to track a virtual page view, a
custom funnel step, or a path that doesn't match the actual browser URL.

---

#### Custom Event Settings

When **Custom Event** is selected:

**Enter Your Event Name Here**
Enter the internal HubSpot behavioral event name. This must match the
event name defined in your HubSpot account under
**Settings → Events → Custom Behavioral Events**.

HubSpot custom behavioral event names follow the format:
`pe{portalId}_{event_internal_name}`

For example: `pe12345678_demo_requested`

**Send Event Properties**
Enable this checkbox if you want to attach properties to the event.
When enabled, an **Event Properties** table appears:

| Column | Description |
|---|---|
| **Property Name** | The internal name of the event property as defined in HubSpot |
| **Property Value** | The value to send — accepts GTM variables |

---

### Optional Config

Expand the **Optional Config** section at the bottom of the tag.

**Enable Console Logging**
When checked, the tag outputs diagnostic messages to the browser console
and GTM Preview Mode. Useful during setup and QA. Disable this before
publishing to production.

---

## Triggering Recommendations

| Use Case | Recommended Trigger |
|---|---|
| Identify on form submission | Custom Event trigger — fire on your form submit event |
| Track a page view on all pages | Page View or DOM Ready trigger |
| Track a page view on virtual pages (SPAs) | History Change trigger |
| Fire a custom event on a button click | Click trigger |
| Identify + event on checkout | Custom Event trigger on your checkout data layer push |

When using **Identify + Event** mode, make sure the trigger fires after
your user data (email, name, etc.) is available in the data layer or DOM.
Firing the tag before the data is ready will result in empty or missing
identity values.

---

## Testing & Validation

Use GTM **Preview Mode** to validate the tag before publishing:

1. Enable **Console Logging** in the Optional Config section.
2. Open GTM Preview Mode and navigate to the page where your trigger fires.
3. Confirm the tag appears in the **Tags Fired** section of the GTM
   debugger panel.
4. Open your browser's developer console and look for `[HubSpot Tag]`
   log entries confirming which calls were made and what data was sent.
5. In HubSpot, check **Reports → Analytics Tools → Tracking Code** to
   verify that page views are being recorded.
6. For identify calls, check the **Contact Activity** feed in HubSpot CRM
   to confirm the contact was identified or updated.
7. For custom behavioral events, check
   **Settings → Events → Custom Behavioral Events** in HubSpot and review
   recent event activity.

Once testing is complete, remember to **disable Console Logging** before
publishing.

---

## Common Issues

**The tag fires in GTM but nothing appears in HubSpot**

The most likely cause is that HubSpot's tracking script has not loaded
before this tag fires. Check your tag firing order and make sure the
HubSpot script tag fires first. You can enforce this using
**Tag Sequencing** in GTM (Tag Configuration → Advanced Settings →
Tag Sequencing → Fire a tag before this tag fires).

**Custom properties are not updating the contact record**

Make sure you are using the internal HubSpot property name, not the
display label. Internal names are lowercase, use underscores, and have
no spaces. You can verify these in **HubSpot → Settings → Properties**.

**Custom behavioral event is not recording**

The event name must match exactly what is configured in HubSpot under
**Custom Behavioral Events**. HubSpot event names are case-sensitive and
must include the full portal ID prefix (e.g. `pe12345678_event_name`).

**Identify call is not associating with the page view**

HubSpot requires a `trackPageView` or `trackCustomBehavioralEvent` call
in the same session to associate an identified visitor with tracked
activity. Enable **Also send event tracking calls** within the
Identify + Event mode, or fire a separate page view tag.

**Email field is empty**

The email address field is required and must resolve to a non-empty value
when the tag fires. If you are pulling the email from a data layer
variable, confirm the data layer push is firing before this tag.

---

## Use Cases

- Identify a contact on form submission and push them into HubSpot CRM
- Track page views on a single-page application where standard HubSpot
  script tracking misses virtual navigations
- Fire custom behavioral events when a user reaches a key milestone
  (e.g. viewed pricing page, started a trial, completed onboarding)
- Enrich HubSpot contact records with custom properties captured during
  a user session
- Combine identify and event tracking in a single GTM tag to reduce
  tag count and simplify your container

---

## License

This template is licensed under the
[Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).

You are free to use, modify, and distribute this template for personal
or commercial purposes. See the `LICENSE` file for full terms.

---

## Contributing

Contributions, bug reports, and feature requests are welcome.
Please open an issue or submit a pull request.

---

## Disclaimer

This template is an independent community contribution and is not
affiliated with, endorsed by, or officially supported by HubSpot, Inc.
or Google LLC. HubSpot's tracking API behavior is subject to change —
always refer to the
[HubSpot Developer Documentation](https://developers.hubspot.com/docs/api/events/tracking-code)
for the latest guidance.
