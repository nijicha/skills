# Pronunciation

The profile's voice speaks one language; walkie:start says which (`Voice: Thai` or `Voice: English`). Write the narration in that language. After each walkie:storyboard run, check the phoneme column. Spell for the ear in the narration only: captions keep the spelling the UI shows.

## Thai (MakHam)

MakHam reads Thai only.

- **Pace.** Each space is a phrase break: every phrase is voiced on its own, with a `phrase_gap` pause (0.12 s) between phrases. Put a space only where a person would breathe, about every 6 to 10 syllables. A space between short words (`การเติมเงิน สปาร์ก`) makes the line choppy.
- **Flags.** `FAST`: over `max_rate` (4.3 syllables a second); add a space where a person would breathe. `CHOPPY`: 3 or more phrases averaging under 4 syllables; join short phrases. `DROPPED`: a sound the model cannot say; respell, or add a lexicon entry.
- **English words.** Letters are spelled out one by one. Write an English UI term in Thai script (`เซฟ`, `คอนเฟิร์ม แอนด์ เซฟ`), or use its Thai meaning.
- **Sounds Thai spelling cannot reach.** Some loanwords need a tone or a final that no spelling gets through the G2P: a product name said สะ-ป๊าร์ก (high tone, final k) is one. Map the word to phonemes in the profile's lexicon (walkie:lexicon), or write phonemes inline once: `{sa↓.paːk↑}`. Tones: `↓` low, `→` mid, `↑` high, `↗` rising, `↘` falling; `.` between syllables.
- **Words the model misreads** (บอกว่า read as bɔː kwaː): reword the line.

## English (Kokoro)

Kokoro reads US English. Each line is voiced in one go, so it paces itself from the punctuation: write normal sentences, with commas where a person would pause. Spaces are not phrase breaks.

- **Flags.** `FAST`: over `max_rate` (5.5 syllables a second); shorten the line or split it into two sentences. `UNKNOWN word`: the G2P does not know the word (product names, people's names, made-up words) and leaves it out of the audio; add a lexicon entry. `DROPPED`: a phoneme the model has no sound for; fix the lexicon entry.
- **Lexicon and inline phonemes.** Map a word to phonemes with walkie:lexicon (`"Eventpop": "ˈɛvəntpˌɑp"`), or write them inline once: `{ˈɛvəntpˌɑp}`. The set is misaki's: `ˈ` primary and `ˌ` secondary stress before the vowel; `A` `I` `O` `W` `Y` for the vowels of day, my, go, now, boy; `ɹ` for r; `ʤ` `ʧ` for the j of jam and the ch of chip. Copy the style of a word the voice already reads right from the phoneme column, and test with walkie:voice_preview.
- **Numbers.** Versions (`0.1.1`, `v2.3`), times (`10:30`) and dates (`28 Nov`, `November 28`) are read as people say them. Other abbreviations may not be: write them out.
- **Names in another language** (Thai names on an English page): say what the viewer needs without them ("the guest's name"), or add them to the lexicon.

## Licences

MakHam and Kokoro are Apache-2.0. Other models may be non-commercial (Wayu Paxa is CC-BY-NC-4.0): check the licence before adding an engine.
