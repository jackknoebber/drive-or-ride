# Drive or Ride

One-page web app. Type a start and destination, and it compares cost and travel time for the Chevy Bolt EUV, the RAV4 Prime on battery, the RAV4 Prime on gas, transit, your bike, and bike + transit. Routes and times come from Google's Routes API, and address suggestions come from Places API (New).

There's no server and no build step. It's just `index.html`. Your API key goes in the app's Settings panel and is saved in your browser, so it's never in the code.

## 1. Get a Google Maps API key (about 10 minutes)

1. Go to https://console.cloud.google.com and create a project, for example "drive-or-ride".
2. **Billing**: link a billing account. Google requires one, but this kind of personal use stays inside the monthly free allowance (thousands of route requests a month).
3. **APIs & Services → Library**: enable **Routes API** and **Places API (New)**.
4. **APIs & Services → Credentials → Create credentials → API key.**
5. Click the new key to edit it:
   - **Application restrictions → Websites**: add your site address once it's live (for example `https://drivetime.knoebber.com/*`). While testing on your own computer, add `http://localhost/*` too.
   - **API restrictions → Restrict key**: tick only Routes API and Places API (New).
6. Optional hard ceiling: under **APIs & Services → Routes API → Quotas**, set "Compute routes requests per day" to something like 200, and do the same for Places autocomplete. Then a bad day can't run up a bill.

## 2. Put it online

The easiest option is Netlify Drop:
1. Go to https://app.netlify.com/drop (a free account is fine).
2. Drag this folder onto the page. You get a URL like `https://something.netlify.app`. You can rename it under Site settings.
3. Add that URL to the key's Website restrictions (step 5 above).

GitHub Pages works too. Push the folder to a repo, then go to Settings → Pages → Deploy from branch.

## 3. First run

Open the site, expand **Settings**, paste the key, and press **Save key**. Add your home address so it shows up as the first suggestion. On your phone, use Share → Add to Home Screen.

## How the numbers work

- **Drive time** is Google's traffic-aware estimate, plus the "Park + walk" minutes you set.
- **Car cost** is distance times energy cost, using the prices and efficiency numbers in Settings, plus parking. Wear and tear is added only if you turn it on.
- **RAV4 battery** uses the battery for the first 42 miles (editable), and the miles after that are charged at the gas rate.
- **Transit fare** uses Google's fare when Google returns one. Otherwise it's $1.75 per direction, since Metro transfers are free for 2 hours. The total is capped at the $5 Metro daily cap.
- **Bike + train** is built by the app, because Google won't plan a trip that mixes your own bike with transit. For each end it picks up to 7 rail stations within bike range (15 mi by default): the nearest few, a couple along the way toward the other end, and any **favorite stations** you list in Settings (Union Station by default). It gets bike times to and from them and train times between every pair (trains only unless you turn that off), then scores the combinations three ways:
  - **Less biking**: a bike minute counts double, so it rides the train as far as it can.
  - **Fastest**: total door-to-door time.
  - **More biking**: a bike minute counts half, so it's happy to bike farther, like riding to Union Station for Metrolink.

  Pick the mix under the results; the app remembers it. Each switch between bike and train adds the "Bike ↔ platform" minutes. One Compare makes roughly 10–15 extra API calls, still well inside the free allowance for personal use, using the Routes and Places APIs you already enabled.
- **Metro daily cap** only applies when the trip is all Metro. Metrolink and Amtrak fares aren't capped.
- **Round trip** doubles the outbound route.
