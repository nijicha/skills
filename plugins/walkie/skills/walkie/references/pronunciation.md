# Pronunciation (MakHam, Thai)

MakHam reads Thai only. Check the phoneme column walkie:storyboard returns after each run.

- **Pace.** Each space is a phrase break: every phrase is voiced on its own, with a `phrase_gap` pause (0.12 s) between phrases. Put a space only where a person would breathe, about every 6 to 10 syllables. A space between short words (`การเติมเงิน สปาร์ก`) makes the line choppy.
- **Flags.** `FAST`: over `max_rate` (4.3 syllables a second); add a space where a person would breathe. `CHOPPY`: 3 or more phrases averaging under 4 syllables; join short phrases. `DROPPED`: a sound the model cannot say; respell, or add a lexicon entry.
- **English.** Letters are spelled out one by one. Write an English UI term in Thai script (`เซฟ`, `คอนเฟิร์ม แอนด์ เซฟ`), or use its Thai meaning.
- **Sounds Thai spelling cannot reach.** Some loanwords need a tone or a final that no spelling gets through the G2P: a product name said สะ-ป๊าร์ก (high tone, final k) is one. Map the word to phonemes in the profile's lexicon (walkie:lexicon), or write phonemes inline once: `{sa↓.paːk↑}`. Tones: `↓` low, `→` mid, `↑` high, `↗` rising, `↘` falling; `.` between syllables.
- **Words the model misreads** (บอกว่า read as bɔː kwaː): reword the line.
- Spell for the ear in the narration only. Captions keep the spelling the UI shows.
- **Licence.** MakHam is Apache-2.0. Other Thai models may be non-commercial (Wayu Paxa is CC-BY-NC-4.0): check the licence before adding an engine.
