# Service / Product Rental Workflow

This document explains the workflow for listing and renting services or
products in the platform.

------------------------------------------------------------------------

# 1. Actors

The system has three main actors:

## Creator

A **User** or **Organization** who lists services or products.

## Renter

A user who wants to rent the service or product.

## Admin

Responsible for reviewing and approving listings.

------------------------------------------------------------------------

# 2. Listing Creation

Creators can list **services or products for rent**.

## Required Information

-   Title
-   Description
-   Category
-   Images
-   Location (for map display)
-   Availability calendar
-   Type (Service or Product)
-   Price
-   Price Type(daily, monthly)

## Listing Status

DRAFT\
PENDING_APPROVAL\
LISTED\
REJECTED\
PAUSED\
REPUBLISH

## Workflow

Creator creates listing\
↓\
DRAFT/Submit listing\
↓\
Status → PENDING_APPROVAL\
↓\
Admin reviews listing\

Admin decision:

Approve → LISTED (visible to public)\
Reject → REJECTED (creator can edit and resubmit)

Pause -> Status product becomes unavailable

Resume -> Listed(if it isn't edited become available/ If edited it goes back to pending_approval)

------------------------------------------------------------------------

# 3. Listing Visibility

Once approved users can:

-   View listing details
-   See images and description
-   View location 
-   Check availability calendar
-   Click **Book Now**
-   View price with price type

------------------------------------------------------------------------

# 4. Booking Process (Manual)

Bookings are **not automatically confirmed**.

Step 1 --- User clicks **Book Now**

User clicks "Book Now"\
↓\
Messenger conversation opens

The renter and creator negotiate:

-   Date
-   Duration
-   Terms
-   Pickup / delivery details

------------------------------------------------------------------------

# 5. Booking Creation

After agreement in chat:

The **creator manually creates the booking**.

Booking data:

-   Renter
-   Start date
-   End date
-   Notes (optional)

------------------------------------------------------------------------

# 6. Availability Logic

Once a booking is created:

Selected dates → Blocked

Effects:

-   Other users cannot book those dates
-   Calendar shows the period as unavailable

If currently booked:

Status → Currently  Active (booked dates unavailable)

------------------------------------------------------------------------

# 7. Booking Lifecycle

Booking statuses:

ACTIVE\
COMPLETED\
CANCELED

Typical flow:

ACTIVE  → COMPLETED / CANCELED

------------------------------------------------------------------------

# 8. During Rental

While the rental is ongoing:

-   Booking status = ACTIVE
-   Listing shows **Unavailable for selected dates**

------------------------------------------------------------------------

# 9. Rental Cancellation

When the item or service is returned:

Owner marks booking → CANCELED

Result:

-   Calendar dates become available again
-   Listing can be rented again

------------------------------------------------------------------------

# 10. Listing Management

Creators can:

-   Edit listing
-   Pause listing
-   Reactivate listing (if edited -> sends for pending approval)
-   Delete listing
-   Add images
-   Update details (sends for pending approval)



------------------------------------------------------------------------

# 11. Complete User Flow

Creator creates listing\
↓\
Admin approves listing\
↓\
Listing becomes visible\
↓\
User clicks "Book Now" (select date range)\
↓\
Chat negotiation\
↓\
Creator manually creates booking \
↓\
Selected dates become unavailable\
↓\
Rental happens (status becomes active)\
↓\
Owner marks as canceled (Remaining booked dates becomes available again)\
↓\
Owner marks booking completed 



------------------------------------------------------------------------

# Core Platform Rules

1.  Listings must be **approved by admin** before being public.
2.  **Booking is manual**, done by the listing owner.
3.  Booked dates are **blocked in the calendar**.
4.  Negotiation happens through **messenger**.
5.  After the rental ends, the owner marks the booking **completed** / **canceled**.

