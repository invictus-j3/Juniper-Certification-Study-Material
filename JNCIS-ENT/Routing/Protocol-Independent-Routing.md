# Protocol Independent Routing

## Static routes

- Default preference of 5
- Qualified-next-hop preference of 7
- Options
  - Discard - Silently drop packet(s) that hit specific route
  - Reject - Drop traffic and send ICMP message
  - Qualified-next-hop

## Aggregate Routes

- Default behavior is to Reject traffic that hits aggregate route
  - Can be changed to discard
- Use __exact detail__ to see type, reject or discard
- Default route preference of 130

## Generated Routes

- Similar to aggregate routes, but more configurable
- Used to generate a route of last resort when conditions are met, generally used for default route
  - Uses a routing policy to identify the conditions
- Can be used to forward traffic, unlike aggregate routes
