# Budget Tracking

How to maintain a trip budget across sessions. The budget lives in `memory/itinerary.md` and is the single source of truth for all financial tracking.

## Budget Table Format

Use the project's home currency (established in Phase 1, stored in CLAUDE.md). Track every expense:

| Category | Item | Cost (Local) | Cost (Home) | Points Used | Charged? | Method | Notes |
|----------|------|-------------|-------------|-------------|----------|--------|-------|

**Categories:** Flights, Trains/Ground, Hotels, Activities, Dining, Misc

**Charged? values:**
- ✅ Yes — money has left the account
- ⏳ At check-in — will be charged on arrival
- ⏳ At travel — charged when service is used
- 🔄 Refundable — charged but can be refunded (note cancellation deadline)
- ❌ Not yet — planned but not booked

**Method:** Credit card name, loyalty points program, cash, etc.

## Points and Loyalty Programs

When travelers use points or miles, track them separately:

- **Points used** column tracks redemptions
- **Calculate value per point**: cash price ÷ points used × 100. This measures redemption value.
- Note the minimum acceptable value per point (varies by program — the user should define this in Phase 1). Don't waste points on poor redemptions.
- Track points balance: started with X, used Y, remaining Z.

## Budget Summary

Maintain a running summary at the bottom of the budget table:

```
### Budget Summary
- **Booked so far:** $X cash + Y points
  - Charged: $A
  - Owed at check-in/travel: $B
- **Estimated unbooked:** $C (dining ~$D + tickets ~$E + transit ~$F + misc ~$G)
- **Estimated trip total:** $H cash + Y points
- **Budget:** $I + J points
- **Buffer:** $K cash + L points
```

Break down "estimated unbooked" into categories so the user can see where remaining spend is expected.

## Refundability Tracking

For travelers who prioritize flexibility, track refundability as a first-class concern:

- Note cancellation deadlines in the Notes column
- For refundable bookings, note the refund policy (full refund until X days before, partial after, etc.)
- When the user's booking philosophy prioritizes refundable options, flag non-refundable bookings clearly

## When to Update

Update the budget after any booking, cancellation, or refund. Also update at session end if financial decisions were made, and when the user asks for a budget check.

## Multi-Currency Handling

- Track costs in both local currency and home currency
- Use exchange rate at time of booking for booked items
- Use a reasonable current estimate for unbooked items
- Note exchange rate source if it matters for planning

## Payment Status Tracking

For trips with complex payment situations, maintain a payment status table in `memory/projects/`:

| Item | Total | Method | Charged | Remaining | When Due |
|------|-------|--------|---------|-----------|----------|
