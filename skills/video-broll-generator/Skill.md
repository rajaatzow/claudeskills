---
name: video-broll-generator
description: Generates cinematic AI video generation prompts (for tools like Sora, Runway, etc.) for b-roll footage. Use this skill whenever a user wants to create b-roll prompts, video prompts, or shot descriptions for a video they're making — especially when they mention a transcript, voiceover, narration, or script. Trigger on phrases like "b-roll prompt", "video b-roll", "generate a shot for", "AI video prompt", "Sora prompt", "Runway prompt", or any request to create visual footage that matches something being said in a video.
---
 
# Video B-Roll Generator
 
Generates a single polished AI video generation prompt for a b-roll shot that visually complements a specific moment in a video's voiceover/narration.
 
## Workflow
 
### Step 1 — Get the transcript
Ask the user for the full transcript of their video. This is the exact spoken narration/voiceover. Example prompt:
> "Please paste your full video transcript (the words you're saying in the video)."
 
### Step 2 — Get the b-roll idea
Ask the user for their b-roll idea. This is what they want to visually show on screen. Example prompt:
> "What's your b-roll idea? Describe the visual you have in mind — it doesn't need to be detailed, just the concept."
 
### Step 3 — Identify the anchor moment
Read the full transcript and find the excerpt that best matches the b-roll idea. The b-roll must make visual and contextual sense while those exact words are being spoken. Identify the specific line(s) from the transcript the b-roll will cover.
 
### Step 4 — Generate the prompt
Using the b-roll idea and the anchor moment from the transcript, fill in all five sections of the template below. Be specific, cinematic, and optimized for AI video generation tools like Sora or Runway.
 
---
 
## Output Template
 
Present the output clearly labeled like this:
 
**Anchor moment:** [the transcript line(s) this b-roll covers]
 
---
 
**B-Roll Prompt:**
 
**[Subject & Action]:** [Describe exactly what is happening. Use strong, specific verbs. Be precise about the subject — not "a person" but "a woman in her 30s" or "a pair of hands". Verbs like: drifts, slices, erupts, glides, snaps, surges — not "walks" or "moves".]
 
**[Environment & Background]:** [Where are we? Be specific — not "a city" but "a rain-slicked Tokyo side street at 2am". State whether background is blurry (shallow depth of field / bokeh) or sharp and in focus.]
 
**[Lighting & Mood]:** [This is the cinematic secret sauce. Describe light source, color temperature, quality (hard/soft), and the emotional feeling it creates. E.g. "warm golden-hour backlight, sun bleeding through dust particles, melancholic and intimate".]
 
**[Camera Movement & Lens]:** [How is the camera moving — or is it still? E.g. "slow push-in on a 85mm lens, slight handheld tremor". Include focal length if it helps.]
 
**[Technical Style Tags]:** [3–6 comma-separated tags that define the overall aesthetic. E.g. "cinematic, 4K, film grain, anamorphic lens flare, shallow DOF, golden hour".]
 
---
 
## Quality Checklist (internal — do not show to user)
 
Before outputting, verify:
- [ ] The b-roll makes clear visual sense while the anchor lines are being spoken
- [ ] Subject & Action uses a strong, specific verb — not generic movement words
- [ ] Environment names a real, specific place or setting (not vague)
- [ ] Lighting describes both the technical quality AND the emotional mood
- [ ] Camera movement includes a lens or focal length reference
- [ ] Technical Style Tags would actually help an AI video model render the right aesthetic
- [ ] The overall prompt reads like a professional cinematographer's shot brief, not a generic description
 
## Notes
 
- The transcript is the spoken voiceover — treat it as the audio layer the b-roll must visually serve
- The user's b-roll idea is the creative seed — honor it, but elevate it with cinematic specificity
- When in doubt about anchor moment, pick the most visually evocative line, not just the closest match
- These prompts are for AI video generation (Sora, Runway, Kling, etc.) — avoid prompting for things AI video currently handles poorly (large crowds, fast text, multiple simultaneous faces)
 
