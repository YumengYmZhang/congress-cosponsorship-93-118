# U.S. Congress cosponsorship data, Congresses 93–118 (screened)

Legislator-by-measure sponsorship and cosponsorship matrices for the U.S. House and Senate, 93rd through 118th Congress (1973–2025), built from Congress.gov records and screened for analysis.

## Files

`data/` holds one R data file per Congress, `93th_bill.RData` through `118th_bill.RData` (the "th" suffix is used for every Congress). Each file loads two objects, `H` (House) and `S` (Senate), and each is a list with:

- `matrix`: numeric legislator × measure matrix. `0` = no relationship, `1` = cosponsor (including a cosponsorship later withdrawn), `2` = principal sponsor. Row names are member display names (same order as `legislator`), column names are measure IDs (same order as `bills`).
- `legislator`: data frame with `id` (Bioguide ID), `name`, `party`, `state`, `tenure`, `party_changed`.
- `bills`: data frame with `bill`, `introduced`, `title`, `area` (policy area), `sponsor.party`, `cosponsors.r`, `cosponsors.d`, `cosponsors.i`, `status` (latest action), `no_sponsor`, `multiple_sponsors`.

"Measures" cover all eight legislation types (bills, joint, concurrent and simple resolutions in each chamber).

```r
load("data/110th_bill.RData")
dim(H$matrix)        # 452 members x 9413 measures
sum(H$matrix == 1)   # House cosponsorships in the 110th Congress
```

`SHA256SUMS.txt` lists a checksum for each file.

## Screening rule

Screening is done separately for each Congress and chamber, for all parties and member types (non-voting delegates are kept). A member is removed if either

- **A:** they cosponsored no retained measure, or
- **B:** they sponsored at least one retained measure and none of their sponsored measures has any cosponsor.

A member who sponsored nothing is not removed under B for that reason alone. When a member is removed, their row and every measure they sponsored are removed (a multiple-sponsor measure is removed if any of its sponsors is). A and B are then recomputed and the step is repeated until no member meets either condition. Being removed in one Congress does not remove that person from any other Congress.

Two rounds were needed: 260 member-Congress rows in the first, 2 in the second. In total 262 rows (236 House, 26 Senate; 212 distinct people) and 1,787 measures were removed. The screened data keep 14,055 member-Congress rows, 360,054 measures, 3,888,992 cosponsorships and 360,060 sponsorships.

## Notes

- `cosponsors.r/d/i` count cosponsors (excluding sponsors) by the party code Congress.gov records on each cosponsorship, recalculated after screening. Independent Democrat (ID) and Libertarian (L) codes are not counted in these three columns, so for a few measures `cosponsors.r + cosponsors.d + cosponsors.i` is below the number of `1`s in that column of `matrix`. Use `sum(matrix == 1)` for full counts.
- The `cosponsor_count_definition` attribute on `bills` was carried over from the unscreened master files and still says "before analysis-roster filtering"; the counts themselves were recalculated for the screened data as described above.
- `no_sponsor` marks measures with no sponsor on Congress.gov; `multiple_sponsors` marks measures with more than one distinct sponsor. `party_changed` marks members with more than one party during their service.

## Members and measures per Congress

| Congress | Years | House members | House measures | Senate members | Senate measures |
|---|---|---|---|---|---|
| 93 | 1973–1975 | 422 | 20,630 | 101 | 5,127 |
| 94 | 1975–1977 | 423 | 18,993 | 100 | 4,914 |
| 95 | 1977–1979 | 425 | 17,591 | 104 | 4,513 |
| 96 | 1979–1981 | 426 | 10,330 | 101 | 4,194 |
| 97 | 1981–1983 | 438 | 9,076 | 101 | 4,064 |
| 98 | 1983–1985 | 432 | 8,073 | 101 | 4,098 |
| 99 | 1985–1987 | 435 | 7,432 | 99 | 4,073 |
| 100 | 1987–1989 | 436 | 7,245 | 101 | 4,009 |
| 101 | 1989–1991 | 442 | 7,585 | 101 | 4,176 |
| 102 | 1991–1993 | 439 | 7,734 | 102 | 4,245 |
| 103 | 1993–1995 | 431 | 6,577 | 101 | 3,175 |
| 104 | 1995–1997 | 433 | 5,265 | 102 | 2,662 |
| 105 | 1997–1999 | 438 | 5,962 | 100 | 3,159 |
| 106 | 1999–2001 | 438 | 6,895 | 101 | 3,897 |
| 107 | 2001–2003 | 438 | 7,004 | 101 | 3,762 |
| 108 | 2003–2005 | 441 | 6,944 | 100 | 3,716 |
| 109 | 2005–2007 | 442 | 8,129 | 101 | 4,920 |
| 110 | 2007–2009 | 452 | 9,413 | 102 | 4,623 |
| 111 | 2009–2011 | 451 | 8,789 | 108 | 4,884 |
| 112 | 2011–2013 | 447 | 7,777 | 101 | 4,462 |
| 113 | 2013–2015 | 442 | 6,903 | 103 | 3,709 |
| 114 | 2015–2017 | 446 | 7,771 | 100 | 4,289 |
| 115 | 2017–2019 | 450 | 8,873 | 105 | 4,680 |
| 116 | 2019–2021 | 446 | 10,570 | 102 | 6,028 |
| 117 | 2021–2023 | 450 | 11,456 | 101 | 6,356 |
| 118 | 2023–2025 | 449 | 12,543 | 104 | 6,759 |

## Source

Retrieved from the Congress.gov API (Library of Congress) in October 2026. Congress.gov suggests, but does not require, the attribution: "Data retrieved from Congress.gov (https://www.congress.gov/)".
