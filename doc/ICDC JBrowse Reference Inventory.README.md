# JBrowse Reference Genome Assets

This inventory records assembly sources, hosted sequence formats, estimated
sizes, index requirements, direct file endpoints, and remaining asset gaps.

The UCSC endpoints are static file URLs used by JBrowse for partial sequence
loading through `TwoBitAdapter`. They are not NCBI metadata API endpoints.

## Reference asset inventory

| Reference | Authoritative assembly source / hosted distribution API | Hosted format | Estimated sequence size | Required index / supporting files | Missing assets / remaining gaps |
| --- | --- | --- | --- | --- | --- |
| CanFam3.1 (`canFam3`) | [NCBI RefSeq GCF_000002285.3](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000002285.3/). The [UCSC canFam3 distribution or API](https://hgdownload.soe.ucsc.edu/goldenPath/canFam3/bigZips/). | `canFam3.2bit` | Approximately **603M**, as listed by UCSC. | Internal 2bit index; configured supporting files: `canFam3.chrom.sizes` and `canFam3.chromAlias.txt`. No FAI/GZI required. | Working configuration reported. Record the UCSC distribution separately from the RefSeq assembly record; identical naming and content must not be assumed. |
| UU_Cfam_GSD_1.0 | [NCBI RefSeq GCF_011100685.1 ](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_011100685.1/); [UCSC hosted distribution or API](https://hgdownload.soe.ucsc.edu/hubs/GCF/011/100/685/GCF_011100685.1/). | `GCF_011100685.1.2bit` | Approximately **622M**, as listed by UCSC. | Internal 2bit index; `GCF_011100685.1.chrom.sizes.txt` and `GCF_011100685.1.chromAlias.txt`. No FAI/GZI required. | All configured assets are listed by UCSC; pasted viewer reported working. Reference-to-BAM compatibility must be recorded separately. |
| ROS_Cfam_1.0 | [NCBI RefSeq GCF_014441545.1](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_014441545.1/); [UCSC hosted distribution or API](https://hgdownload.soe.ucsc.edu/hubs/GCF/014/441/545/GCF_014441545.1/). | `GCF_014441545.1.2bit` | Approximately **601M**, as listed by UCSC. | Internal 2bit index; `GCF_014441545.1.chrom.sizes.txt` and `GCF_014441545.1.chromAlias.txt`. No FAI/GZI required. | All configured assets are listed by UCSC; pasted viewer reported working. Verify whether BAMs require ERCC or other additions absent from this distribution. |


## Direct JBrowse file endpoints

### CanFam3.1 (`canFam3`)

| Asset | Endpoint |
| --- | --- |
| Reference sequence | <https://hgdownload.soe.ucsc.edu/goldenPath/canFam3/bigZips/canFam3.2bit> |
| Chromosome sizes | <https://hgdownload.soe.ucsc.edu/goldenPath/canFam3/bigZips/canFam3.chrom.sizes> |
| Chromosome aliases | <https://hgdownload.soe.ucsc.edu/goldenPath/canFam3/bigZips/canFam3.chromAlias.txt> |

### UU_Cfam_GSD_1.0

| Asset | Endpoint |
| --- | --- |
| Reference sequence | <https://hgdownload.soe.ucsc.edu/hubs/GCF/011/100/685/GCF_011100685.1/GCF_011100685.1.2bit> |
| Chromosome sizes | <https://hgdownload.soe.ucsc.edu/hubs/GCF/011/100/685/GCF_011100685.1/GCF_011100685.1.chrom.sizes.txt> |
| Chromosome aliases | <https://hgdownload.soe.ucsc.edu/hubs/GCF/011/100/685/GCF_011100685.1/GCF_011100685.1.chromAlias.txt> |

### ROS_Cfam_1.0

| Asset | Endpoint |
| --- | --- |
| Reference sequence | <https://hgdownload.soe.ucsc.edu/hubs/GCF/014/441/545/GCF_014441545.1/GCF_014441545.1.2bit> |
| Chromosome sizes | <https://hgdownload.soe.ucsc.edu/hubs/GCF/014/441/545/GCF_014441545.1/GCF_014441545.1.chrom.sizes.txt> |
| Chromosome aliases | <https://hgdownload.soe.ucsc.edu/hubs/GCF/014/441/545/GCF_014441545.1/GCF_014441545.1.chromAlias.txt> |


## JBrowse usage and verification

- For UCSC-hosted references, use `TwoBitAdapter` with `twoBitLocation` and `chromSizesLocation`.
- Use `RefNameAliasAdapter` with the corresponding chromosome-alias URL to resolve equivalent sequence names.
- For self-hosted S3 references, use `IndexedFastaAdapter` with the FASTA file and its matching FAI index.
- Associate each selected BAM or VCF file with its compatible reference sequence and load it alongside its matching index in JBrowse.
- A configured reference is not necessarily compatible with every BAM or VCF file. Compare the file header’s sequence names and lengths against the selected reference, check for custom additions, and document any discrepancies.
- UCSC provides public URLs for the reference sequences. Hosting compatible copies in an ICDC-managed S3 bucket would improve robustness by providing an alternative source if UCSC becomes unavailable. Automatic fallback is not currently implemented.

## Currently configured references

The supplied frontend configuration declares the following assembly names:

```javascript
export const assemblyNames = [
  'canFam6', 'canFam5', 'canFam4', 'canFam3', 'canFam2', 'canFam1',
];
```

| Reference / configuration entry | Recorded status |
| --- | --- |
| `canFam3` / CanFam3.1 | Existing UCSC 2bit assembly configuration supplied; includes chromosome sizes and chromosome aliases. |
| `canFam6`, `canFam5`, `canFam4`, `canFam2`, `canFam1` | Declared in `assemblyNames`. Their assembly objects, exact accession versions, sequence URLs and compatibility evidence are detected scanning through header of all bam or vcf files in production. |
| `UU_Cfam_GSD_1.0` | Reference sequences are available through UCSC file URLs or a self-hosted S3 bucket. Both options require configuration. |
| `ROS_Cfam_1.0` | Reference sequences are available through UCSC file URLs or a self-hosted S3 bucket. Both options require configuration. |


## Annotation compatibility or Discrepancy: CanFam3.1 only

The reference sequence and the additional annotation tracks are separate assets.
Based on the supplied configuration, the three active
`jBrowseOptions.additionalTracks` target **CanFam3.1 (`canFam3`) coordinates**.
They must not be reused automatically for UU_Cfam_GSD_1.0, ROS_Cfam_1.0 or other
assemblies. Loading a different reference sequence does not convert annotation
coordinates.

| Active additional track | Target reference | Format / adapter | Data and index endpoints |
| --- | --- | --- | --- |
| NCBI RefSeq (GFF3) | CanFam3.1; filename identifies `GCF_000002285.3` | BGZF-compressed GFF3 plus TBI; `Gff3TabixAdapter` | [GFF3](https://d3qlumquwycjrs.cloudfront.net/annotations/GCF_000002285.3_CanFam3.1_genomic.sorted.gff.gz) / [TBI](https://d3qlumquwycjrs.cloudfront.net/annotations/GCF_000002285.3_CanFam3.1_genomic.sorted.gff.gz.tbi) |
| Ensembl (GFF3) | `canFam3` | BGZF-compressed GFF3 plus TBI; `Gff3TabixAdapter` | [GFF3](https://d3qlumquwycjrs.cloudfront.net/annotations/canFam3.ensGene.sorted.gff.gz) / [TBI](https://d3qlumquwycjrs.cloudfront.net/annotations/canFam3.ensGene.sorted.gff.gz.tbi) |
| Liftover - hg38 genes mapped to canFam3 (GFF3) | `canFam3`; hg38 is the source of the mapped genes, not the target coordinates | BGZF-compressed GFF3 plus TBI; `Gff3TabixAdapter` | [GFF3](https://d3qlumquwycjrs.cloudfront.net/annotations/hg38tocanFam3.sorted.gff.gz) / [TBI](https://d3qlumquwycjrs.cloudfront.net/annotations/hg38tocanFam3.sorted.gff.gz.tbi) |

## **Resolve Configuration discrepancy:** 
The independent UU and ROS viewers should load their reference sequence and
the selected matching BAM/index pair without these CanFam3.1 annotation tracks.
**Compatible UU/ROS annotation tracks** should be identified and added separately if available.


## Documented differences and follow-up inventory

| Difference / gap | Evidence and required follow-up |
| --- | --- |
| Annotation compatibility | Reference sequences are available for CanFam3.1, UU, and ROS, but the existing annotation tracks support CanFam3.1 only. Keep those tracks disabled for UU and ROS unless compatible annotations are added. |
| Reference and track compatibility | Load only the reference sequence compatible with the selected BAM/VCF files, along with annotation tracks verified for that reference. |
| Interoperability between reference and annotation sources | Verify that the UCSC CanFam3.1 reference (`GCA_000002285.2`) and NCBI RefSeq annotations (`GCF_000002285.3`) use compatible sequence names, lengths, and coordinates. Document any required chromosome aliases or unresolved differences. |
| ERCC, lambda or other custom additions | Base assembly names and chromosome aliases do not establish inclusion of custom sequences. Record the affected file UUIDs, extra sequence names/lengths, source versions and required custom reference assets. |
| Other legacy assembly names | `canFam1`, `canFam2`, `canFam4`, `canFam5` and `canFam6` need exact assembly definitions and asset evidence to be fully inventoried. |
| Unresolved file-to-reference differences | For each sampled BAM/VCF with an unexplained reference mismatch, record the study, file UUID, header evidence, sequence differences, and required follow-up. |

## Technical references

- [JBrowse assembly configuration and sequence adapters](https://jbrowse.org/jb2/docs/config_guides/assemblies/)
- [UCSC 2bit format specification](https://genome.ucsc.edu/FAQ/FAQformat.html#format7)
- [NCBI CanFam3.1 annotation report](https://www.ncbi.nlm.nih.gov/refseq/annotation_euk/Canis_lupus_familiaris/105/)
