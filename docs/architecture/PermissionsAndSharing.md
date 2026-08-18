# Permissions and Sharing

## Purpose

This document separates identity, authorisation, and future sharing. It gives the MVP a secure ownership model without prematurely building caregiver invitations or a veterinarian portal.

## Core terms

- **Authentication:** The backend establishes the signed-in user's identity.
- **Authorisation:** Backend middleware and application services determine whether that identity can perform an action on a pet and its data.
- **Membership:** A pet-scoped relationship that grants a user an allowed role.
- **Owner:** The user who creates the pet and initially controls its membership.

Authentication does not grant access by itself. A user must have an allowed membership for the relevant pet.

## MVP access model

The MVP supports a single owner membership per pet in the user-facing product.

- Creating a pet creates its owner membership.
- The owner can read and manage that pet and all of its pet-scoped journal data.
- A user cannot view or modify another user's pet, even if they know its identifier.
- A pet can be archived but remains accessible to its owner according to the data-retention policy.

The data model uses memberships from the beginning so that future sharing does not require moving events or changing the journal's security boundary.

## Future role model

Sharing is deferred from the MVP. When introduced, the expected role model is:

| Role | Intended capability |
| --- | --- |
| Owner | Manage pet, memberships, settings, data export, archive, and all journal data. |
| Caregiver/editor | Read pet data and create/edit journal data within the agreed policy; cannot manage ownership or members. |
| Viewer | Read Journal and Reports data; cannot change it. |
| Veterinarian | A deliberately designed read-only role or time-limited share, not a shortcut around membership rules. |

Exact edit/delete restrictions, invite flow, expiry, and activity/audit history are future product decisions. They must be finalised before these roles are exposed.

## Data ownership and lifecycle

- Pet and journal data belong to the pet owner for product-authorisation purposes.
- A membership grants access; it does not transfer ownership.
- Revoking a membership removes future access but does not erase events the former member legitimately created. Audit metadata remains subject to the retention policy.
- Ownership transfer, deletion, and deceased-pet handling need explicit user flows before implementation.
- Full JSON export is available to the owner as a portability and backup mechanism.

## Security requirements

- Backend middleware and application services enforce membership checks for every pet-scoped request.
- The client must not rely on hidden buttons or route guards as a security control.
- Users cannot create their own elevated membership, alter another owner's membership, or reassign an event to a pet they cannot access.
- Repository methods require an authenticated user context and expose authorisation failures clearly without leaking another pet's data.
- Future share links must be expiring, revocable, minimally scoped, and implemented as a distinct access model—not as unauthenticated API access.

## Privacy implications

Pet medical journals may reveal household routines and contact information. Future sharing must include clear consent, recipient identity, scope, and revocation. Product analytics or support tooling must not expose private journal contents without a separately approved privacy policy.

## Open decisions

- Whether caregivers can edit or delete others' entries.
- Whether veterinarians receive a membership, an expiring report link, or both.
- Invitation channel and account requirements.
- Ownership-transfer and account-deletion process.
- Audit log visibility to owners.
