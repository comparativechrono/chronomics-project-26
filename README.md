# One repeat, many genomes – the PER3 VNTR project

**A guided undergraduate research project of the Chronomic Medicine Group, in a workbench that runs in the
browser.** The student reads a 54-base tandem repeat (VNTR) in exon 19 of the clock gene *PER3* in the 462
long-read haplotypes of the Human Pangenome Reference Consortium, describes its alleles unit by unit, finds a
common SNP that tags it, uses the tag to test the repeat in genome-wide association studies of chronotype and
sleep in UK Biobank and in brain gene expression (GTEx), and traces the repeat through 27 other primates.
It needs no programming experience: the guide teaches the Linux terminal, `samtools`/`bcftools`, R and the
genome browser as the project needs them.

The site is built from the [Bioinformatics workbench](https://github.com/comparativechrono/bioinformatics-workbench)
of the same group: the same notebooks (R and Python), terminal, Files tab, genome browser (igv.js), Databases tab,
3D viewer, IDEs and AI assistant – with the programs and tabs the project does not need left out, and a guide in
the left pane in place of the workbench's help (the help is still there, behind **Help**).

---

## Contents

1. [The project in brief](#the-project-in-brief)
2. [The guide](#the-guide)
3. [Put it online](#put-it-online)
4. [Before the student starts](#before-the-student-starts)
5. [Editing the guide](#editing-the-guide)
6. [The data](#the-data)
7. [How it was tested](#how-it-was-tested)
8. [What is different from the Bioinformatics workbench](#what-is-different-from-the-bioinformatics-workbench)
9. [Files](#files)
10. [Credits and licences](#credits-and-licences)

---

## The project in brief

**Question.** For twenty years small studies have linked the four- and five-unit alleles of the *PER3* VNTR to
chronotype, delayed sleep phase, sleep homeostasis and cognition after sleep loss, with mixed results. Large GWAS
could not test it: the repeat (216–270 bp of near-identical 54-bp units) cannot be read with SNP arrays or short
reads. Long-read pangenomes can read it directly – and if a SNP tags it, every study that measured the SNP has, in
effect, measured the repeat.

**Plan.** Eleven stages, about one a week (10–12 weeks part-time). Stages 1–7 and 9 are the core; 8 and 10 add a
lot; each stage ends with "going further" ideas, so the student can make the project her own.

| Stage | Title | The student learns |
| --- | --- | --- |
| 0 | Start here; the science; the data; the reading list; glossary | the circadian clock, *PER3* and the claims made for the repeat; how to keep a logbook |
| 1 | The terminal and the genome | shell commands, paths, pipes; FASTA, `.fai`, GFF3 and BED (and 0- vs 1-based coordinates); `samtools faidx`; the genome browser; a REST API with `curl`/`jq` |
| 2 | Find the repeat | k-mer counting, `grep -ob`, regular expressions, `fold`; translating by hand; the protein units and their serines and threonines |
| 3 | From a pangenome to 462 haplotypes | VCF, genotypes and phasing; `bcftools view/query/consensus`; shell loops and variables |
| 4 | Count the repeats | turning FASTA into a table; checking a result two ways; first R notebook: data frames, `table`, `barplot`, saving figures; genotypes |
| 5 | What makes the alleles different | writing R functions; string handling; the genetic code as a lookup table; synonymous and missense changes |
| 6 | Who carries which allele | joining tables; frequencies with confidence intervals; chi-squared and Hardy–Weinberg tests; bar charts with error bars |
| 7 | A SNP that travels with the repeat | linkage disequilibrium and r²; rsIDs with `bcftools annotate`; matrices and `apply` |
| 8 | 3,202 short-read genomes | concordance against a truth set; frequencies in 26 populations; what short reads see of a repeat |
| 9 | Does the repeat change sleep? | GWAS summary statistics; GRCh37 vs GRCh38; strand-ambiguous SNPs; confidence intervals and power; regional plots; multiple testing; eQTLs; an approximate conditional analysis |
| 10 | The repeat in apes and monkeys | translation in three frames; tolerant regular expressions; `kalign` and `FastTree`; reasoning about evolution on a tree |
| 11 | Write it up | figures, methods with versions, results with uncertainty, discussion; turning notebooks into scripts and a `run_all.sh` |

As far as we know the analysis is new: the repeat's alleles in a long-read pangenome, its tag SNPs across
populations, and the test of the repeat in the largest studies of sleep. **A supervisor's answer key is not in
this repository** (it is public, and the student would find it): the results that the guide's *Check* boxes give
are there to confirm each step, and the interpretation is left to the student.

## The guide

The left pane has a row of tabs: **Start**, the stages **1–11**, and **Help** (the manual of the tools). Each
stage page has: what the student will learn; the science where needed; numbered steps with grey code boxes
(**Run in the terminal** or **Add to notebook**); green **Check** boxes with the numbers she should get; *Your
turn* tasks with hidden hints and answers; *Think* questions; a logbook box; *going further*; and a checklist,
whose ticks are kept in the browser and fill in the stage's tab. Each stage with R has its own notebook
(`~/notebooks/stageNN-….ipynb`, made by a button), and the student keeps a logbook, `~/project/logbook.md`.

Early stages give the code and explain it line by line; later ones ask her to write more of it herself, with the
code behind *Show the answer*. Her results go into `~/project/results` and `~/project/figures`, and stage 11 turns
the work into scripts that rebuild everything.

## Put it online

1. In this repository on GitHub: *Settings → Pages → Build and deployment*: Source **Deploy from a branch**,
   branch **main**, folder **/ (root)**. Save.
2. After a minute the site is at **https://comparativechrono.github.io/chronomics-project-26/**. The first time a
   browser opens it, the page loads once more by itself: it installs a small service worker (`isolation-sw.js`)
   that lets PLINK 2 use threads (nothing else in the project needs it).

The repository must be public for GitHub Pages on a free plan. To try it on your own computer: `python3 serve.py
8000`, then open `http://localhost:8000` (opening `index.html` as a file does not work).

## Before the student starts

- **Computer.** A laptop or desktop with a current Chrome, Edge, Firefox or Safari; 8 GB of memory is comfortable.
- **Network.** Besides this site, the browser must reach `webr.r-wasm.org`, `repo.r-wasm.org` and
  `cran.r-universe.dev` (R and its packages), `cdn.jsdelivr.net` (only if she opens a Python notebook), and the
  services the guide uses: `rest.ensembl.org`, `rest.uniprot.org`, `www.ebi.ac.uk` (InterPro, BLAST, Europe PMC),
  `alphafold.ebi.ac.uk`, `eutils.ncbi.nlm.nih.gov`, `api.genome.ucsc.edu` and `gtexportal.org`.
- **Her files live in her browser** – not on a server: the same computer and browser each time, and **My files →
  Download all my files** once a week (the guide says so). A private window, or clearing the browser's data, loses
  them. The site keeps its files apart from the Bioinformatics workbench's (`storePrefix: 'chrono26'` in
  `assets/js/config.js`), although both are on `comparativechrono.github.io`.
- **The AI assistant** (optional) needs a key of her own (Google Gemini has a free tier); the guide tells her to
  check what it says and to declare its use. Decide whether you want her to use it.
- **Public data only.** All the data are public and anonymous; the guide tells her never to put personal or
  patient data into the workbench or an AI tool.

## Editing the guide

The page is built from `build/`. After any change there, run:

```bash
python3 build/make_page.py        # writes index.html; warns about links to pages that do not exist
```

- **Guide pages**: `build/help/00-home.html` (Start), `00a-science.html`, `00b-data.html`, `00c-reading.html`,
  `00d-glossary.html` and the stages `00s01-genome.html` … `00s11-writeup.html`. The first line of each holds its
  id, title, group (`The project`), `stage: N`, and `tab: yes` for the page that a stage's tab opens. Checklists
  are `<ul class="checklist"><li data-check="unique-id">…</li></ul>`; the classes of the boxes (`callout check`,
  `think`, `log`, `science`, `task`, `details.hint`) are styled in `assets/css/guide.css`, and the tabs,
  checklists and stage-notebook buttons are run by `assets/js/guide.js`.
- **Tool help**: the other pages of `build/help/` (the workbench's, trimmed to this project; `23-vcf.html` is new).
- **Starter notebooks**: `python3 build/make_starters.py` writes `data/starter/*.ipynb` (`start-here.ipynb` opens
  first).
- **Settings**: `assets/js/config.js` (title, the cards of the Databases tab, R and AI settings).
- **Programs**: after a change in `assets/vendor/`, run `python3 build/vendor_sums.py`.

## The data

`data/examples/per3/` (about 7.5 MB) holds everything the project needs, described in
[data/examples/per3/README.md](data/examples/per3/README.md) and in the guide (*The project data*): GRCh38 around
*PER3* with its genes and dbSNP; the HPRC release 2 pangenome (231 people) at the repeat and across 500 kb; the
1000 Genomes high-coverage phased panel (3,202 people) across *PER3*; six UK Biobank sleep GWAS (GRCh37) for
600 kb; GTEx v8 eQTL results for the tag; *PER3* in 28 primates and 53 species. How each file was made – the
commands and the addresses – is in [data-build/](data-build/), and most of the scripts there were re-run and
reproduce the files exactly. The other folders of `data/examples/` are small examples kept from the workbench
for its help pages.

## How it was tested

On 21 October 2026, in Chromium (Playwright): every code box of the guide (stages 1–11, the first session and
the VCF page) was run in order in the workbench – terminal boxes in the terminal as the *Run in the terminal*
button runs them, R boxes both with `Rscript` and, for stage 4, through **Add to notebook** in the stage's notebook
(plots, `png()` files, tables) – and gave the numbers of the *Check* boxes; the same code was also run with native
`samtools`, `bcftools` and R, with the same results. The stage tabs, checklists, stage notebooks, logbook, genome
browser buttons and the page layout were checked. The analysis behind the guide was done first, independently,
in Python and R. The AI assistant was not tested with a real key.

## What is different from the Bioinformatics workbench

- **Left out**: the Galaxy and Networks tabs, and the programs for read QC, mapping, RNA-seq, single-cell,
  somatic and metagenomic analyses, peak calling, gene finding and HMMER (their files are not in
  `assets/vendor/`, and `assets/js/project-tools.js` takes them out of the terminal); the example data that went
  with them; most Databases cards (`databases:` in `config.js`).
- **Kept**: samtools, bcftools, bgzip/tabix, seqtk, minimap2, bedtools, gffread, jq, kalign, MUSCLE, FastTree,
  seq-align, LASTZ, wgsim, PLINK 2, the GNU tools, Python and R.
- **Added**: the guide (stage tabs, checklists, stage notebooks, logbook), the project's data and its
  `data-build/`, the genome browser's default genome (the project's piece of GRCh38 with *PER3*), a help page on
  VCF files, and *PER3* examples in the Databases tab.

## Files

```
index.html             the page (built by build/make_page.py)
isolation-sw.js        the service worker that makes the page cross-origin isolated (keep it next to index.html)
assets/js/             the workbench; guide.js (the guide), project-tools.js (the programs of this project), config.js
assets/css/            styles; guide.css
assets/vendor/         bundled programs and libraries, with their licences (see THIRD_PARTY.md)
data/examples/per3/    the project's data, and its README.md
data/examples/         small example files kept from the workbench
data/starter/          the starter notebooks
data-build/            how the project's data were made
build/                 make_page.py, help/ (the guide and the help pages), template.html, citations.json,
                       make_starters.py, vendor_sums.py
serve.py               a small local web server (optional)
THIRD_PARTY.md         the third-party software and data, their licences and sources
```

## Credits and licences

Built from the Bioinformatics workbench of the Chronomic Medicine Group. The bundled programs and libraries keep
their own licences – see [THIRD_PARTY.md](THIRD_PARTY.md), and keep `assets/vendor/licenses/` with the site. The
data are public (sources and terms in THIRD_PARTY.md and the data README); the studies and resources to cite are
listed in the guide (*The project data*). The guide and the site's own code and text are by the Chronomic Medicine
Group.
