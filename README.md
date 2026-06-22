# Grapheme-to-Phoneme (G2P) for the Luxembourgish language

This repository contains the FOMA source code and associated source files for building a Luxembourgish grapheme-to-phoneme (G2P) converter. This G2P is part of the two Luxembourgish voices, **Mil** and **Mia**, developed for the [RHVoice](https://rhvoice.org/) project.

If you want to know more about the Luxembourgish voices, how to install and configure them, please consult this article: [Two new voices for reading Luxembourgish](https://accessibilite.public.lu/en/news/2026-03-30-screenreaderLB.html).

The G2P converts written Luxembourgish into a phonetic transcription that drives the speech synthesizer. It handles tokenization, abbreviations, compounds, numbers, spelling, lexicon lookup, and the phonological rules used to pronounce out-of-lexicon words.

## Requirements

- [Foma](https://fomafst.github.io/) — the finite-state toolkit used to compile the `.foma` sources.
- Python 3 — for `convert-lexicon.py`, the lexicon-conversion helper.

## Build

The `.foma` scripts are compiled with Foma and emit FSTs in AT&T (`.att`) format, which are the artifacts consumed by RHVoice. Each script writes its output to `/tmp` (see the `write att` line at the end of each one):

```sh
# Main G2P rules  ->  /tmp/g2p.att
foma -f g2p.foma

# Compile the main lexicon  ->  /tmp/lex.att
foma -f lex.foma

# Compile the secondary ("French hack") lexicon  ->  /tmp/sl_lex.att
foma -f sl_lex.foma
```

The lexicons are stored in Foma's space-separated text format (`lex.txt`, `sl_lex.txt`) so they compile quickly even with many entries. To regenerate `lex.txt` from a raw, tab-separated `word<TAB>pronunciation` lexicon:

```sh
python3 convert-lexicon.py my-raw-lexicon.txt
```

This normalizes casing (keeping an upper-case entry only when its pronunciation differs from the lower-case form) and writes `lex.txt` in the current directory.

> **Note:** files such as `lex.lex`, `sl.lex`, and `tmp` / `tmp2` are generated/scratch artifacts, not hand-edited sources.

## Repository structure

| File(s) | Purpose |
| --- | --- |
| `g2p.foma` | Main G2P entry point: composes the phonological rules. |
| `alphabet.foma`, `symbols.foma`, `onsets.foma`, `syl.foma` | Phonological building blocks (alphabet, symbols, syllable onsets, syllabification). |
| `tok.foma`, `tok_config.foma`, `tok_subs.foma`, `tok_sreader_spell.foma` | Tokenization and text substitutions. |
| `numbers.foma`, `number_defns.foma`, `numeral_names.foma`, `num_dict.foma`, `romans.foma` | Number, numeral and Roman-numeral expansion. |
| `abbrevs.foma`, `compounds.foma`, `spell.foma`, `lseq.foma`, `downcase.foma` | Abbreviations, compound words, spelling, letter sequences, case folding. |
| `gpos.foma`, `qst.foma`, `phrasing.tree`, `pitch-mod.tree` | Part-of-speech / question handling and prosody (phrasing, pitch) models. |
| `x-sampa.foma`, `sl_phone_mapping.foma`, `SpokenSymbols.foma`, `msg.foma` | Phoneme mapping (X-SAMPA), spoken symbols and messages. |
| `emoji.foma` | Emoji name expansion. |
| `lex.foma`, `lex.txt` | Main Luxembourgish lexicon (source + compiled). |
| `sl_lex.foma`, `sl_lex.txt` | Secondary lexicon used for French words. |
| `enwords.foma`, `enwords.txt` | English word handling. |
| `convert-lexicon.py` | Converts a raw tab-separated lexicon into Foma's spaced text format. |

## Related repositories

- [RHVoice-Luxembourgish-bin](https://github.com/accessibility-luxembourg/RHVoice-Luxembourgish-bin) — the corresponding compiled files.
- [RHVoice-Mil](https://github.com/accessibility-luxembourg/RHVoice-Mil) — binary HTS voice model for Mil.
- [RHVoice-Mia](https://github.com/accessibility-luxembourg/RHVoice-Mia) — binary HTS voice model for Mia.

## Contributing

The G2P is continuously refined, mostly to correct pronunciations. If you notice a mispronunciation, please open an issue describing the word, the wrong pronunciation, and the expected one.

## License

This software is published by the [Information and press service](https://sip.gouvernement.lu/en.html) of the Luxembourg Government and licensed under the GNU LGPL 2.1 license. See [LICENSE](LICENSE).
