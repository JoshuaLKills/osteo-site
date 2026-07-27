// Creates a Stripe Checkout Session server-side. The client never sets the
// price — every price is looked up or recomputed here from the same catalog
// and formula used on the site, so a tampered request can't checkout for
// less than the real price.
const Stripe = require('stripe');

// ---- Catalog: mirrors the 6 real products on the site (index.html) ----
// unitAmount is in cents (USD).
const CATALOG = {
  'marrow-grip-kettlebell-shell': { name: 'Marrow Grip Kettlebell Shell', unitAmount: 8900 },
  'cortex-dumbbell-handle':       { name: 'Cortex Dumbbell Handle',       unitAmount: 3400 },
  'trabecula-j-cup-insert':       { name: 'Trabecula J-Cup Insert (pair)', unitAmount: 2200 },
  'sternum-collar-set':           { name: 'Sternum Collar Set (pair)',    unitAmount: 2800 },
  'radius-anchor-plate':          { name: 'Radius Anchor Plate',          unitAmount: 1900 },
  'periosteum-massage-roller':    { name: 'Periosteum Massage Roller',    unitAmount: 4200 },
};

// ---- Configurator ("Build Yours"): mirrors the pricing chips in index.html ----
const CONFIG_PARTS = {
  'Dumbbell handle':          { basePrice: 34, category: 'rigid' },
  'Kettlebell shell':         { basePrice: 89, category: 'rigid' },
  'J-cup insert':             { basePrice: 22, category: 'rigid' },
  'Barbell collar':           { basePrice: 28, category: 'rigid' },
  'Bench pad':                { basePrice: 58, category: 'soft' },
  'Barbell cushion sleeve':   { basePrice: 24, category: 'soft' },
  'Deadlift drop pad':        { basePrice: 46, category: 'soft' },
};

const CONFIG_MATERIALS = {
  'PETG':               { mult: 0.7,  fits: 'rigid' },
  'PETG-CF':             { mult: 1,    fits: 'rigid' },
  'Nylon-CF':            { mult: 1.35, fits: 'rigid' },
  'Nylon-GF':            { mult: 1.15, fits: 'rigid' },
  'PC-CF':               { mult: 1.75, fits: 'rigid' },
  'TPU blend':           { mult: 0.85, fits: 'both' },
  'Antimicrobial TPU':   { mult: 1.05, fits: 'both' },
  'PEBA-Air':            { mult: 1.55, fits: 'soft' },
};

function computeConfigPriceCents(part, material, density) {
  const p = CONFIG_PARTS[part];
  const m = CONFIG_MATERIALS[material];
  if (!p || !m) return null;
  if (m.fits !== 'both' && m.fits !== p.category) return null; // invalid combo, refuse
  const d = Math.max(15, Math.min(100, Math.round(Number(density))));
  if (!Number.isFinite(d)) return null;
  const price = Math.round(p.basePrice * m.mult * (0.75 + d / 120));
  return price * 100; // to cents
}

exports.handler = async function (event) {
  if (event.httpMethod !== 'POST') {
    return { statusCode: 405, body: 'Method not allowed' };
  }

  let payload;
  try {
    payload = JSON.parse(event.body || '{}');
  } catch (e) {
    return { statusCode: 400, body: JSON.stringify({ error: 'Invalid JSON' }) };
  }

  const items = Array.isArray(payload.items) ? payload.items : [];
  if (items.length === 0) {
    return { statusCode: 400, body: JSON.stringify({ error: 'Cart is empty' }) };
  }

  const stripeKey = process.env.STRIPE_SECRET_KEY;
  if (!stripeKey) {
    return { statusCode: 500, body: JSON.stringify({ error: 'Stripe is not configured on the server yet.' }) };
  }
  const stripe = Stripe(stripeKey);

  const line_items = [];

  for (const raw of items) {
    const qty = Math.max(1, Math.min(20, parseInt(raw.qty, 10) || 1));

    if (raw.type === 'catalog') {
      const entry = CATALOG[raw.slug];
      if (!entry) {
        return { statusCode: 400, body: JSON.stringify({ error: `Unknown product: ${raw.slug}` }) };
      }
      line_items.push({
        quantity: qty,
        price_data: {
          currency: 'usd',
          unit_amount: entry.unitAmount,
          product_data: { name: entry.name },
        },
      });
    } else if (raw.type === 'custom') {
      const cents = computeConfigPriceCents(raw.part, raw.material, raw.density);
      if (cents === null) {
        return { statusCode: 400, body: JSON.stringify({ error: 'Invalid custom part configuration' }) };
      }
      const label = `Custom ${raw.part} — ${raw.material}${raw.colorName ? ', ' + raw.colorName : ''}`;
      line_items.push({
        quantity: qty,
        price_data: {
          currency: 'usd',
          unit_amount: cents,
          product_data: { name: label },
        },
      });
    } else {
      return { statusCode: 400, body: JSON.stringify({ error: 'Unknown item type' }) };
    }
  }

  const origin = event.headers.origin || process.env.URL || 'https://osteo.gym';

  try {
    const session = await stripe.checkout.sessions.create({
      mode: 'payment',
      line_items,
      success_url: `${origin}/?checkout=success`,
      cancel_url: `${origin}/?checkout=cancel`,
      shipping_address_collection: { allowed_countries: ['US', 'CA', 'PR'] },
    });
    return { statusCode: 200, body: JSON.stringify({ url: session.url }) };
  } catch (err) {
    return { statusCode: 500, body: JSON.stringify({ error: err.message || 'Stripe error' }) };
  }
};
