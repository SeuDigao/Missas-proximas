# missas-proximas

A [Claude Code](https://claude.ai/code) skill that finds nearby Catholic mass times for any location in Brazil.

## Overview

Ask naturally about mass times — "tem missa hoje?", "missa perto de mim", "missa em Gramado" — and the skill locates Catholic churches within 5 km, finds their schedules from official parish websites and social media, and returns the next upcoming masses sorted by time, with distance and source confidence.

## Platform

**Designed for [Claude Code](https://claude.ai/code)** (CLI or IDE extension). This skill uses Claude Code tools (`Bash`, `WebSearch`, `WebFetch`) and will not run in mobile Claude or other environments.

> The [original skill](https://github.com/SeuDigao/Hor-rios-de-missas) was built for the mobile Claude app. This is a Claude Code adaptation that replaces mobile-only tools (`user_location_v0`, `places_search`, `user_time_v0`) with their Claude Code equivalents.

## How It Works

1. **Location** — detects your approximate city via IP geolocation (`ipapi.co`). If you name a city or neighborhood, uses that instead.
2. **Time** — gets the current date and time via `Bash date`.
3. **Churches** — searches for Catholic parishes, chapels, and sanctuaries within 5 km using `WebSearch` and `WebFetch`.
4. **Schedules** — fetches mass times from parish websites, diocesan sites, and social media pages.
5. **Result** — lists upcoming masses sorted by time, with distance, confidence level (confirmed / a confirmar), and links to sources.

## Trigger Phrases

The skill activates on any of these (no need to invoke it explicitly):

- "tem missa hoje?"
- "missa perto de mim"
- "onde tem missa agora?"
- "missa em [cidade]"
- "missa no bairro [X] de [cidade]"
- "horário de missa", "missa domingo", "próxima missa"
- "paróquia", "chapel", "celebração católica"

## Parameters

| Parameter | Default | Description |
|---|---|---|
| Location | IP geolocation | City/neighborhood/address named by the user, or detected by IP |
| Radius | 5 km | Distance from reference point; overridable ("até 10 km", "só no bairro") |
| Window | Today + tomorrow | Time range; overridable ("missa de domingo", "missa na sexta") |

## Output Format

```
Próxima missa: 18h30 (daqui a 40 min), Capela São José, 1,2 km.

Hoje (quinta, 12/11):
• 18h30, Capela São José (capela), 1,2 km, confirmado
• 19h30, Matriz N. Sra. de Fátima (paróquia), 2,8 km, confirmado
• 19h00, Capela Santo Antônio, 4,1 km, a confirmar

Amanhã:
• 7h00, Matriz N. Sra. de Fátima, 2,8 km

Redes: Matriz N. Sra. de Fátima: @paroquiafatima (Instagram)
```

## Honesty Rules

The skill never fabricates mass times. Each result is marked:
- **confirmado** — sourced from an official parish site or recent post
- **a confirmar** — from an indirect or undated source

When a schedule is uncertain and the mass is imminent (under 1 hour), the skill recommends confirming by phone or social media before heading out.

## Installation

The skill is already installed if this file is in your `~/.claude/skills/missas-proximas/` folder. To install on another machine, copy the entire `missas-proximas/` folder there and restart Claude Code.

## Files

```
SKILL.md    — Skill definition and runtime instructions
README.md   — This file
```

## Credits

Original skill by [SeuDigao](https://github.com/SeuDigao/Hor-rios-de-missas).
Claude Code adaptation by Rodrigo Fernandes.
