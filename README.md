# Homebase API integration and MCP server: connect Homebase to ChatGPT, Claude and any AI agent

**Homebase API integration and MCP server by Cotlet.dev: yes, Claude, ChatGPT and other AI agents can work in your Homebase account.** Homebase has no official MCP server and no ChatGPT or Claude app; it offers a read-only API key on its top plan, and its own Assistant is a waitlist product (checked 26 Nov 2026). Cotlet.dev connects your own signed-in Homebase account to any AI agent, so you ask in plain words and the AI helps you build the schedule, review timecards before payroll, split tips and compare labor with sales.

## What you can ask

> Build next week's schedule from my team's availability and approved time off, and show me the conflicts and overtime risks. Publish only after I say yes.

> List all timecards for the pay period with missed clock outs, edits and overtime, and fix nothing yet.

> Export this pay period's approved timesheets for Gusto and tell me which employees failed to match.

> Calculate tonight's tip pool shares per person from the sales and hours, and show the total I owe each person.

> Compare last week's labor cost with sales by day and flag days where labor ran over the target.

## What owners and managers of small restaurants, cafes and bars ask their AI to do in Homebase

From owners and managers of small restaurants, cafes and bars' posts on Reddit, forums, review sites and the Homebase help center (checked 26 Nov 2026).

| Job | Ask your AI | Make it repeat |
|---|---|---|
| Build next week's schedule | "Build next week's schedule from my team's availability and approved time off, and show me the conflicts and overtime risks. Publish only after I say yes." | When you ask |
| Review timesheets before payroll | "List all timecards for the pay period with missed clock outs, edits and overtime, and fix nothing yet." | Weekly |
| Hours to Gusto or QuickBooks | "Export this pay period's approved timesheets for Gusto and tell me which employees failed to match." | Your own endpoint, called from a sheet or n8n |
| Tips split after close | "Calculate tonight's tip pool shares per person from the sales and hours, and show the total I owe each person." | Daily |
| Labor cost against sales | "Compare last week's labor cost with sales by day and flag days where labor ran over the target." | Weekly |
| Hiring: post, screen, book interviews | "Write a job post for a line cook at this location, publish it to the job boards, and list applicants the AI screener rated best." | When you ask |
| Shift swaps without arguments | "List every shift swap and offered shift from last week, who agreed, and who actually worked." | When you ask |
| Time off and PTO balances | "Show PTO and sick leave balances for each employee and the requests pending approval." | When you ask |

Your AI asks you before it sends, books, replies or changes anything. Payments stay in your hands.

## Homebase API

Homebase gives the account owner a read-only API key on the All in One plan, requested in Settings under API Access; it cannot add or change anything in Homebase (checked 26 Nov 2026). With Cotlet.dev you build your own Homebase API: show your AI agent a job once in your signed-in Homebase account, save it as an endpoint and call it from n8n, Make, Zapier or your own code. How it works: https://cotlet.dev/integrations/api/homebase

Endpoints you can build in a few minutes (none of them exists until you build it):

- `timesheet-check`: Missed clock outs, edits and overtime for a pay period
- `tip-shares`: Tip pool shares per person for a day
- `labor-vs-sales`: Labor cost against sales per day, with the days over target
- `pto-balances`: PTO and sick leave balances with the requests pending approval
- `publish-job-post`: Post a job to the job boards after your OK

Call one with your Cotlet API key: `POST https://cotlet.dev/api/v1/custom/timesheet-check`. One call runs up to 90 seconds, writes wait for your OK, payments are blocked.

## Connect in 2 minutes

Get your API key at https://cotlet.dev/app and export it as `COTLET_API_KEY`.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

### ChatGPT

Settings → Apps → add connector URL `https://cotlet.dev/mcp/v1`, then sign in with Cotlet.dev. No API key needed.

### Cursor

Copy `.cursor/mcp.json` from this repo into your project:

```json
{
  "mcpServers": {
    "cotlet": {
      "url": "https://cotlet.dev/mcp/v1",
      "headers": {
        "Authorization": "Bearer ${COTLET_API_KEY}"
      }
    }
  }
}
```

## Run it locally (stdio)

This repo is a real MCP server. It speaks MCP over stdio, lists its Homebase tools without any key, and forwards every tool call to the hosted Cotlet.dev server. The hosted URL above stays the recommended way. Use this when your client only starts local servers.

```json
{"mcpServers":{"homebase":{"command":"uvx","args":["homebase-mcp"],"env":{"COTLET_API_KEY":"your-key"}}}}
```

Or with Docker:

```bash
docker build -t homebase-mcp .
docker run -i --rm -e COTLET_API_KEY homebase-mcp
```

Tools: `homebase_guide`, `homebase_status`, `homebase_open`, `homebase_read`, `homebase_click`, `homebase_type`, `homebase_close`. Without `COTLET_API_KEY` a tool call answers: "Set COTLET_API_KEY. Get a key at https://cotlet.dev/app".

## Command line

Python 3.10+, nothing else to install. From PyPI:

```bash
pip install homebase-mcp        # or run it once: uvx homebase-mcp connect
homebase-mcp connect
```

Or straight from this repo:

```bash
export COTLET_API_KEY="your-key"
python3 homebase_agent.py connect   # ready config for Claude Code, Cursor, ChatGPT
python3 homebase_agent.py status    # is Homebase ready in your Cotlet.dev account
python3 homebase_agent.py guide     # Homebase screens and sign-in link
python3 homebase_agent.py read      # open Homebase, read the page, close the browser
python3 homebase_agent.py tools     # list Cotlet.dev tools
python3 homebase_agent.py serve     # start the stdio MCP server (same as no argument)
```

## Questions from Homebase users

**Does Homebase have an API?**

Homebase gives the account owner a read-only API key on the All in One plan, requested in Settings under API Access; it cannot add or change anything in Homebase (checked 26 Nov 2026). With Cotlet.dev you build your own Homebase API on your signed-in account and call it from n8n, Make or your own code: https://cotlet.dev/integrations/api/homebase

**How do I connect Homebase to ChatGPT?**

Add Cotlet.dev as an app in ChatGPT (Settings → Apps, connector URL `https://cotlet.dev/mcp/v1`), sign in to Homebase once in your Cotlet.dev Cloud Browser, and ask ChatGPT in plain words. No code and no Homebase API key.

**Is there a way to use Claude with Homebase?**

Yes. In Claude Code run the one-line `claude mcp add` command above; in the Claude app add `https://cotlet.dev/mcp/v1` as a custom connector. Claude then works in your own Homebase account.

**Is there a Homebase MCP server?**

Homebase has no official MCP server for your account. Cotlet.dev is a hosted MCP server that works in your own Homebase account for you, with Claude, ChatGPT, Cursor, Codex, n8n, Make and Zapier.

**Can AI change my schedule in Homebase through the API key?**

No. The Homebase API key is read only and cannot add or change anything. Cotlet.dev works in your signed-in Homebase account on the Schedule and Timesheets screens, and writes wait for your OK.

**Does Homebase have an official MCP server?**

No (checked 26 Nov 2026). An independent server from usefulapi is listed in the MCP Registry and says it is not affiliated with Homebase. It reads with your own read-only API key.

## Find it on

- [Homebase on Cotlet.dev](https://cotlet.dev/integrations/mcp/homebase?utm_source=github&utm_medium=readme&utm_campaign=homebase&utm_content=find)
- [PyPI package homebase-mcp](https://pypi.org/project/homebase-mcp/)
- [Odel](https://odel.app/modules/cotlet/homebase)
- [mcpbeat](https://mcpbeat.com/mcp-servers/cotlet/homebase/)

## Also works with

- [Mangomint MCP](https://github.com/cotlet-connect/mangomint-mcp)
- [Wanderlog MCP](https://github.com/cotlet-connect/wanderlog-mcp)
- [SmugMug MCP](https://github.com/cotlet-connect/smugmug-mcp)
- [Garmin Connect MCP](https://github.com/cotlet-connect/garmin-mcp)
- [Housecall Pro MCP](https://github.com/cotlet-connect/housecall-pro-mcp)
- [Medium MCP](https://github.com/cotlet-connect/medium-mcp)
- [Crexi MCP](https://github.com/cotlet-connect/crexi-mcp)
- [DistroKid MCP](https://github.com/cotlet-connect/distrokid-mcp)
- [LinkedIn MCP](https://github.com/cotlet-connect/linkedin-mcp)
- [EstimateOne MCP](https://github.com/cotlet-connect/estimateone-mcp)
- [Google Search Console MCP](https://github.com/cotlet-connect/google-search-console-mcp)
- [Lichess MCP](https://github.com/cotlet-connect/lichess-mcp)
- [Strava MCP](https://github.com/cotlet-connect/strava-mcp)
- [StreamElements MCP](https://github.com/cotlet-connect/streamelements-mcp)
- [Meetup MCP](https://github.com/cotlet-connect/meetup-mcp)
- [AccuRanker MCP](https://github.com/cotlet-connect/accuranker-mcp)
- [GetYourGuide MCP](https://github.com/cotlet-connect/getyourguide-mcp)
- [Idealista MCP](https://github.com/cotlet-connect/idealista-mcp)
- [Riverside MCP](https://github.com/cotlet-connect/riverside-mcp)
- [Udemy MCP](https://github.com/cotlet-connect/udemy-mcp)
- [Faire MCP](https://github.com/cotlet-connect/faire-mcp)
- [Teachable MCP](https://github.com/cotlet-connect/teachable-mcp)
- [Vagaro MCP](https://github.com/cotlet-connect/vagaro-mcp)
- [Buildertrend MCP](https://github.com/cotlet-connect/buildertrend-mcp)
- [Zoopla MCP](https://github.com/cotlet-connect/zoopla-mcp)
- [Dribbble MCP](https://github.com/cotlet-connect/dribbble-mcp)
- [Hostelworld MCP](https://github.com/cotlet-connect/hostelworld-mcp)
- [Mashvisor MCP](https://github.com/cotlet-connect/mashvisor-mcp)
- [Rent Manager MCP](https://github.com/cotlet-connect/rent-manager-mcp)
- [ThriveCart MCP](https://github.com/cotlet-connect/thrivecart-mcp)
- [Fresha MCP](https://github.com/cotlet-connect/fresha-mcp)
- [Booksy MCP](https://github.com/cotlet-connect/booksy-mcp)
- [BookingKoala MCP](https://github.com/cotlet-connect/bookingkoala-mcp)
- [Milanote MCP](https://github.com/cotlet-connect/milanote-mcp)
- [Partiful MCP](https://github.com/cotlet-connect/partiful-mcp)
- [Contractor Foreman MCP](https://github.com/cotlet-connect/contractor-foreman-mcp)
- [Pixieset MCP](https://github.com/cotlet-connect/pixieset-mcp)
- [GlossGenius MCP](https://github.com/cotlet-connect/glossgenius-mcp)
- [ZenMaid MCP](https://github.com/cotlet-connect/zenmaid-mcp)
- [AroFlo MCP](https://github.com/cotlet-connect/aroflo-mcp)
- [Simpro MCP](https://github.com/cotlet-connect/simpro-mcp)
- [Workiz MCP](https://github.com/cotlet-connect/workiz-mcp)
- [Kickserv MCP](https://github.com/cotlet-connect/kickserv-mcp)
- [Service Autopilot MCP](https://github.com/cotlet-connect/service-autopilot-mcp)
- [Mindbody MCP](https://github.com/cotlet-connect/mindbody-mcp)
- [DaySmart MCP](https://github.com/cotlet-connect/daysmart-mcp)
- [Acuity Scheduling MCP](https://github.com/cotlet-connect/acuity-scheduling-mcp)
- [Jane App MCP](https://github.com/cotlet-connect/jane-app-mcp)
- [FieldEdge MCP](https://github.com/cotlet-connect/fieldedge-mcp)
- [Phorest MCP](https://github.com/cotlet-connect/phorest-mcp)
- [DINGG MCP](https://github.com/cotlet-connect/dingg-mcp)
- [Invoice Simple MCP](https://github.com/cotlet-connect/invoice-simple-mcp)
- [PhotoShelter MCP](https://github.com/cotlet-connect/photoshelter-mcp)
- [RazorSync MCP](https://github.com/cotlet-connect/razorsync-mcp)
- [ShootProof MCP](https://github.com/cotlet-connect/shootproof-mcp)
- [Invoice2go MCP](https://github.com/cotlet-connect/invoice2go-mcp)
- [Appointy MCP](https://github.com/cotlet-connect/appointy-mcp)
- [Contractor+ MCP](https://github.com/cotlet-connect/contractor-plus-mcp)
- [Petpooja MCP](https://github.com/cotlet-connect/petpooja-mcp)
- [Teachers Pay Teachers MCP](https://github.com/cotlet-connect/teachers-pay-teachers-mcp)
- [Bloom MCP](https://github.com/cotlet-connect/bloom-mcp)
- [hipages MCP](https://github.com/cotlet-connect/hipages-mcp)
- [ServiceSeeking MCP](https://github.com/cotlet-connect/serviceseeking-mcp)
- [MyJobQuote MCP](https://github.com/cotlet-connect/myjobquote-mcp)
- [MyBuilder MCP](https://github.com/cotlet-connect/mybuilder-mcp)
- [Yelp for Business MCP](https://github.com/cotlet-connect/yelp-for-business-mcp)
- [Rosie MCP](https://github.com/cotlet-connect/rosie-mcp)
- [Roofr MCP](https://github.com/cotlet-connect/roofr-mcp)
- [FieldPulse MCP](https://github.com/cotlet-connect/fieldpulse-mcp)
- [YourTradebase MCP](https://github.com/cotlet-connect/yourtradebase-mcp)
- [JobNimbus MCP](https://github.com/cotlet-connect/jobnimbus-mcp)
- [Ascora MCP](https://github.com/cotlet-connect/ascora-mcp)
- [eLocal MCP](https://github.com/cotlet-connect/elocal-mcp)
- [Alto MCP](https://github.com/cotlet-connect/alto-mcp)
- [Rightmove Plus MCP](https://github.com/cotlet-connect/rightmove-plus-mcp)
- [TurboTenant MCP](https://github.com/cotlet-connect/turbotenant-mcp)
- [Innago MCP](https://github.com/cotlet-connect/innago-mcp)
- [Property Finder MCP](https://github.com/cotlet-connect/property-finder-mcp)
- [LoopNet MCP](https://github.com/cotlet-connect/loopnet-mcp)
- [Dotloop MCP](https://github.com/cotlet-connect/dotloop-mcp)
- [SkySlope MCP](https://github.com/cotlet-connect/skyslope-mcp)
- [ShowingTime MCP](https://github.com/cotlet-connect/showingtime-mcp)
- [connectMLS MCP](https://github.com/cotlet-connect/connectmls-mcp)
- [WebPT MCP](https://github.com/cotlet-connect/webpt-mcp)
- [Tebra MCP](https://github.com/cotlet-connect/tebra-mcp)
- [ChiroTouch MCP](https://github.com/cotlet-connect/chirotouch-mcp)
- [ChiroFusion MCP](https://github.com/cotlet-connect/chirofusion-mcp)
- [Semble MCP](https://github.com/cotlet-connect/semble-mcp)
- [Nookal MCP](https://github.com/cotlet-connect/nookal-mcp)
- [Cliniko MCP](https://github.com/cotlet-connect/cliniko-mcp)
- [Gentu MCP](https://github.com/cotlet-connect/gentu-mcp)
- [splose MCP](https://github.com/cotlet-connect/splose-mcp)
- [Physitrack MCP](https://github.com/cotlet-connect/physitrack-mcp)
- [ALLDATA MCP](https://github.com/cotlet-connect/alldata-mcp)
- [Mitchell 1 ProDemand MCP](https://github.com/cotlet-connect/mitchell1-mcp)
- [Identifix MCP](https://github.com/cotlet-connect/identifix-mcp)
- [Worldpac speedDIAL MCP](https://github.com/cotlet-connect/worldpac-mcp)
- [RepairLink MCP](https://github.com/cotlet-connect/repairlink-mcp)
- [PartsTech MCP](https://github.com/cotlet-connect/partstech-mcp)
- [Fullbay MCP](https://github.com/cotlet-connect/fullbay-mcp)
- [Shopmonkey MCP](https://github.com/cotlet-connect/shopmonkey-mcp)
- [HotSchedules MCP](https://github.com/cotlet-connect/hotschedules-mcp)
- [Sling MCP](https://github.com/cotlet-connect/sling-mcp)
- [Tanda MCP](https://github.com/cotlet-connect/tanda-mcp)
- [Rotaready MCP](https://github.com/cotlet-connect/rotaready-mcp)
- [Clover MCP](https://github.com/cotlet-connect/clover-mcp)
- [Epos Now MCP](https://github.com/cotlet-connect/eposnow-mcp)
- [SpotOn MCP](https://github.com/cotlet-connect/spoton-mcp)
- [Olo MCP](https://github.com/cotlet-connect/olo-mcp)
- [SevenRooms MCP](https://github.com/cotlet-connect/sevenrooms-mcp)
- [All apps connected through Cotlet.dev](https://github.com/cotlet-connect)

---

More: https://cotlet.dev/integrations/mcp/homebase?utm_source=github&utm_medium=readme&utm_campaign=homebase

Start free: https://cotlet.dev/signup?app=homebase&utm_source=github&utm_medium=readme&utm_campaign=homebase
