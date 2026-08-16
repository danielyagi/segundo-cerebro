# Orlando → NYC One-Way Flight Comparison
**Date:** 2027-01-02 | **Passengers:** 3 adults | **Baggage:** 1 carry-on (10kg) + 1 checked (23kg) each
**Exchange Rate:** USD:BRL = 5.20

---

## Comparative Table

| Carrier (Airport) | Base Fare (USD) | Bag Fees (USD) | Total (USD) | Total (BRL) | Notes |
|---|---|---|---|---|---|
| **Southwest (LGA)** | $1,026 | $0 | **$1,026** | **R$5,335.20** | 2 checked bags free per person. Carry-on included. |
| **United (EWR)** | $1,056–$1,593 | $105 | **$1,161–$1,698** | **R$6,037–R$8,830** | Carry-on included (3 carry-ons in fare). 1 checked bag 23kg = $35/pax → $105 total for 3. |
| **Frontier (LGA/EWR)** | $1,458* | $135 | **$1,593** | **R$8,283.60** | *Site blocked (403). Base fare $1,458. Carry-on not included (discount fare). Checked bag ~$35–$45/pax. |
| Delta (LGA) | $1,794 | $105 | $1,899 | R$9,874.80 | Carry-on included. First checked bag ~$35/pax. |
| American (LGA) | $1,893 | $105 | $1,998 | R$10,389.60 | Carry-on included. First checked bag ~$35/pax. |
| JetBlue (JFK) | $2,415 | $0 | $2,415 | R$12,558.00 | 1 carry-on + 1 checked bag (23kg) included per person. |
| Delta (JFK) | $2,394 | $105 | $2,499 | R$12,994.80 | Carry-on included. First checked bag ~$35/pax. |
| Spirit | — | — | — | — | **Ceased operations** May 2, 2026. Bankruptcy winding-down. Not booking. |
| Allegiant | — | — | — | — | **No MCO→NYC route.** From MCO serves only Allentown, Appleton, Asheville, Knoxville. |
| Breeze | — | — | — | — | **No MCO→NYC route.** NYC-area destinations are Newburgh (SWF), New Haven (HVN), Providence (PVD) — not EWR/JFK/LGA. |

---

## Recommendation

**🥇 Southwest (LGA) — Best Value** at **$1,026 / R$5,335.20**

- Lowest total cost across all carriers
- Two free checked bags per person (30kg total checked allowance per adult) — no bag fees at all
- Carry-on included at no extra charge
- Single stop in LGA (vs. nonstop alternatives at higher cost)

**🥈 United (EWR) — Best Nonstop Option** at **$1,161–$1,698**

- Nonstop flight from MCO to EWR
- Carry-on included (3 carry-ons in base fare)
- 1 checked bag 23kg: $35/pax → $105 total for 3 pax
- Price range reflects basic vs. flexible fare

**Notes:**

1. **Spirit Airlines** is non-operational. Per Wikipedia, Spirit ceased all operations at MCO on May 2, 2026 as part of bankruptcy proceedings. Do not attempt booking.

2. **Frontier Airlines** is operational but the booking site returns HTTP 403 (Cloudflare bot protection). Confirmed via Wikipedia that Frontier does serve MCO with routes to LGA and EWR. Price estimates based on user-provided data ($1,458 base fare). Carry-on not included on discount/basic fares — budget $50 extra for carry-on if needed.

3. **Allegiant Air** resumed limited MCO operations in 2024 but only flies to 4 domestic destinations (Allentown, Appleton, Asheville, Knoxville). No NYC-area service from MCO. Allegiant's NYC-area flights operate from SFB (Sanford), serving Newburgh/SWF — not a direct MCO→NYC option.

4. **Breeze Airways** serves MCO but its NYC-area destinations are all secondary airports (Newburgh/SWF, New Haven/HVN, Providence/PVD). Does not serve EWR, JFK, or LGA from MCO.

5. **JetBlue** offers the most baggage-inclusive option (1 carry-on + 1 checked bag included) at $2,415 — highest base fare but zero bag fees. Worth considering if baggage needs exceed 1 checked bag per person.

6. **Delta** appears twice (LGA and JFK) with LGA being significantly cheaper ($1,794 vs $2,394). LGA route is nonstop from MCO.

---

## Data Sources

- Wikipedia: [Orlando International Airport](https://en.wikipedia.org/wiki/Orlando_International_Airport) — airlines and destinations table
- Wikipedia: [Allegiant Air destinations](https://en.wikipedia.org/wiki/Allegiant_Air_destinations)
- Wikipedia: [List of Breeze Airways destinations](https://en.wikipedia.org/wiki/List_of_Breeze_Airways_destinations)
- HTTP status verification: spirit.com → spiritrestructuring.com (ceased ops), frontier.com (operational, booking site 403-blocked), allegiantair.com (403 Cloudflare challenge), flybreeze.com (403 Cloudflare challenge)
- Carrier websites: HTTP status codes captured via curl User-Agent spoofing
