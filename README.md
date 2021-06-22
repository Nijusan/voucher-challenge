# Vouchers
Aufgabe ist es, eine API zur Bestellung von Voucher-Codes zu erstellen. Die API nimmt eine E-Mail-Adresse an, reserviert einen freien hinterlegten Voucher und versendet eine double opt in (DOI) E-Mail. Sobald das DOI bestätigt wurde, wird der reservierte Voucher an die angegebene E-Mail-Adresse versendet.

Die Vouchers (Liste wird geliefert) müssen in das System importiert werden.

Die API sollte auch parallelen Aufrufen standhalten.

## API Dokumentation

### 1. Reserve
`POST /api/participants`

#### Input
Name | Type | Description
--- | --- | ---
`email` | `String` | email address of customer

#### Example
```json
{ "email": "test@example.com" }
```

#### Response

> Status: 200 OK
```json
{ "status": "received" }
```

#### Possible responses
Status | Description
--- | ---
`{ "status": "received" }` | Everything OK, an email with a confirmation token/link will be send
`{ "status": "error", "errors": { "email": "duplicate" }}` | Duplicate email
`{ "status": "error", "errors": { "email": "invalid_format" }}` | Invalid email


### 2. Redeem
`POST /api/participants/confirm`

#### Input
Name | Type | Description
--- | --- | ---
`token` | `String`| The token the customer got with the first email

#### Example
```json
{ "token": "jKbu9e" }
```

#### Response
> Status: 200 OK
```json
{ "status": "confirmed" }
```

#### Possible responses
Status | Description
--- | ---
`{ "status": "confirmed" }` | Everything OK, it is now enqueued to be delivered
`{ "status": "already_confirmed" }` | Already confirmed, it will NOT be delivered again
`{ "status": "not_confirmable" }` | State doesn't allow confirmation, e.g. tried to confirm, BEFORE the DOI email was sent
`{ "status": "invalid" }` | Invalid token or token not found
`{ "status": "no_vouchers" }` | No more free vouchers available
