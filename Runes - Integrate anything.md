A rune is one action that you can take against a system (usually a 3rd party service) or a reaction your system can have to activity in that system.

## Typical actions

- List domain entities
- Get specific domain entity by identifier
- Create domain entity
- Change domain entity
- Trigger effect/run command in domain

## Typical reactions

- New entity in domain
- Entity changed in domain
- Effect or command occurred in domain

## Typical action mechanisms

- HTTP API
- WebSocket API
- gRPC API
- GraphQL API
- Other API

## Typical reaction mechanisms

- Polling on schedule/Cron mechanism against endpoint meant for updates
- Polling on schedule/Cron mechanism against list entities endpoint and comparing held state to find updates
- Webhook events (receive request) on change
- WebSocket server-side event

## Runebook / Runeset / Rune

If you are building a support for Runes operating on Stripe you would make a Runebook for Stripe or for a subset like Stripe Payments. The Runeset is a domain entity, like Payment. Each Rune is a reaction or action relevant for that domain entity, such as "Received payment (reaction)", "Refund payment (action)".

