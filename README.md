# cim-oeo-alignment
Files for aligning the terms of the Open Energy Ontology (OEO) with the terms of the Common Information Model (CIM). This ontology alignment is created by NFDI4Energy.

The alignment is being documented according to [SSSOM](https://mapping-commons.github.io/sssom/dev/) (Simple Standard for Sharing Ontology Mappings). The alignment consists of a set of two files:
- `sssom-cim-oeo-header.yml`: a YAML file containing metadata for the alignment/mapping set.
- `cim-oeo.tsv`: a tsv file containing the alignments/mappings themselves.

## Terminology Sources

The vocabulary terms and definitions used for this alignment can be found here:
- OEO: <https://openenergyplatform.org/viewer/oeo/>
- CIM: <https://modellen.netbeheernederland.nl/tc57cim/cim17/schema/index.html>

## Notes on Terminology

- mapping vs alignment: We prefer the term *alignment* for the artifacts in this repository, rather than *mapping*. However, as SSSOM uses the term *mapping* in its documentation, we use both interchangeably in this repository in an attempt to reduce confusion when referencing the instructions in the SSSOM documentation.
   - The reason for preferring *alignment* over *mapping* is because the OEO and CIM have very different use cases - OEO is a reference ontology, and CIM is an application ontology. With their substantial differences in scope and usage, a direct, one-to-one mapping of terms between these vocabularies is not possible. What is possible is a looser association of terms, in which multiple CIM terms roughly correlate to a single OEO term at a higher level of abstraction (e.g. the CIM terms "WindGenTurbineType1aIEC", "WindGenTurbineType2IEC", and "WindGenType3IEC" are all related through the predicate `skos:broadMatch` to the OEO term "wind rotor").
- Each line in the tsv file is a single *mapping* or *alignment*. The entire tsv file is a *mapping set* or *alignment set*.

## tsv File Fields

1. **subject_id**
   - The CURIE for the source of the mapping.
   - For this alignment, CIM is used as the subject of each mapping.
2. **subject_label**
   - A human-readable label for the source of the mapping.
   - This should always match the `rdfs:label` annotation of the CIM term.
3. **predicate_id**
   - The relation between the subject/source and the object/target.
   - `skos:broadMatch` indicates that the OEO term is broader than the CIM term.
   - `skos:narrowMatch` indicates that the OEO term is narrower than the CIM term.
   - Refer to [this page](https://mapping-commons.github.io/sssom/dev/spec-model/#allowed-and-common-mapping-predicates) for a list of common predicates and basic usage guidelines.
   - Refer to [this page](https://mapping-commons.github.io/sssom/dev/mapping-predicates/) for more detailed usage guidance (e.g. differences between "related", "close", "exact", "narrow" and "broad" in SKOS terminology).
4. **object_id**
   - The CURIE for the target of the mapping.
   - For this alignment, the OEO is used as the object of each mapping.
5. **object_label**
   - A human-readable label for the target of the mapping.
   - This should always match the `rdfs:label` annotation of the OEO term.
6. **mapping_justification**
   - The method used to obtain the mapping.
   - Refer to [this page](https://mapping-commons.github.io/sssom/dev/mapping-justifications/) for an overview of allowable entries for this field.
   - Use `semapv:ManualMappingCuration` if the mapping was created by a human based on their domain knowledge (this will likely be the case for most, if not all, mappings in this partiuclar mapping set).
7. **confidence**
   - A number between 0 and 1 indicating the mapping author's confidence in the accuracy of the mapping.
   - 0 indicates 0%/no confidence. 1 indicates 100%/full confidence.
8. **author_id**
   - A CURIE referencing the person who created the mapping.
   - ORCIDs are preferred (e.g. `orcid:0009-0009-2960-3474`). 
9. **comment**
   - Any additional comments regarding the mapping.

## Notes on SSSOM Usage

- Abbreviations for the CURIEs in the mapping set are all listed under `curie_map` in the YAML header file.
- SSSOM **cannot** model complex mappings where entities are mapped to multiple terms (e.g. "A is a close match to 'B with condition C'").
- It is allowed to create multiple mappings for a single subject and a single predicate (e.g. "A is a related match to B" and "A is a related match to C").
- If no suitable entity is found in the OEO to map to a given CIM term, or in CIM to map to a given OEO term, create a mapping with the value `sssom:NoTermFound` as demonstrated here:
  
    | subject_id        | subject_label | predicate_id    | object_id         | object_label |
    |-------------------|---------------|-----------------|-------------------|--------------|
    | cim:TermA         | TermA         | skos:exactMatch | sssom:NoTermFound |              |
    | sssom:NoTermFound |               | skos:exactMatch | oeo:99990001      | TermB        |
    |                   |               |                 |                   |              |

- There are no concrete rules for determining the confidence level of a manually created mapping; the creator should use their best judgement. More information from https://mapping-commons.github.io/sssom/dev/mapping-justifications/:
  
    > [confidence](https://mapping-commons.github.io/sssom/dev/confidence/) is an incredibly useful metric for downstream users, including ETL engineers and data analysts. In an ideal world, all mappings have some kind of confidence associated with them. confidence scores should be read as "the strength of evidence provided in this record/table row (i.e mapping justification) leads us to believe the mapping (e.g. OMOP:44499396 --[skos:broadMatch]--> OMOP:4028717) is correct with 90% confidence.
    > 
    > In manual curation, confidence expresses the domain expertise degree of conviction that the asserted mapping holds true. While manual mapping curation is still considered a gold standard, in practice human agents have (a) varying levels of expertise on the subject domain, (b) varying levels of understanding of the intuitions behind "semantic spaces" and associated concepts and (c) varying levels of metadata associated with a concept to be able to determine a match (definitions, labels, papers, synonyms, etc). Documenting confidence can be very useful both to increase the transparency of data science pipelines that involve entity mappings, and as a means to increase curation speed: rather than trying to achieve 100% confidence for a mapping, which can be extremely time-consuming, it is often better to first "wave through" a mapping with lower confidence to reach coverage, and later revisit low confidence mappings iteratively.
