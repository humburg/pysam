SAM=$(wildcard *.sam)
BAM=$(SAM:%.sam=%.bam)
BAI=$(BAM:%.bam=%.bam.bai)
CRAM=ex1.cram ex2.cram ex3.cram
CRAI=$(CRAM:%.cram=%.cram.crai)

# ex2.bam - bam file without index

all: ex1.pileup.gz \
	ex1.sam ex1.bam \
	ex2.sam.gz ex2.sam ex2.bam ex2.bam.bai \
        uncompressed.bam \
	$(BAM) $(BAI) \
	$(CRAM) $(CRAI) \
	example_bai.bam \
        rg_with_tab.bam \
	ex2_truncated.bam \
	empty.bam empty.bam.bai \
	faidx_empty_seq.fq.gz

# ex2.sam - as ex1.sam, but with header
ex2.sam.gz: ex1.bam ex1.bam.bai
	samtools view -h ex1.bam | gzip > ex2.sam.gz

#%.bam: %.sam ex1.fa.fai
#	samtools import ex1.fa.fai $< $@

uncompressed.bam: ex2.sam
	samtools view -buS $< > $@

%.bam: %.sam
	samtools view -bS $< > $@

%.cram: %.sam
	samtools view -bC -T ex1.fa $< > $@

%.cram.crai: %.cram
	samtools index $<

%.sam: %.sam.gz
	gunzip < $< > $@

ex1.fa.fai:ex1.fa
		samtools faidx ex1.fa
ex1.bam:ex1.sam.gz ex1.fa.fai
		samtools import ex1.fa.fai ex1.sam.gz ex1.bam

%.bam.bai:%.bam
		samtools index $<

ex1.pileup.gz:ex1.bam ex1.fa
		samtools mpileup -f ex1.fa ex1.bam | gzip > ex1.pileup.gz

ex2_truncated.bam: ex2.bam
	head -c 124000 ex2.bam > ex2_truncated.bam

empty.bam: ex2.sam
	grep "^@" $< | samtools view -Sb - > $@

example_unmapped_reads_no_sq.bam: example_unmapped_reads_no_sq.sam
	touch tmp.list
	samtools import tmp.list $< $@
	rm -f tmp.list

example_bai.bam: ex1.bam
	cp ex1.bam $@
	samtools index $@
	mv $@.bai example_bai.bai

clean:
	rm -fr *.bam *.bai *.fai *.pileup* \
		*~ calDepth *.dSYM pysam_*.sam \
	ex2.sam ex2.sam.gz ex1.sam
	*.fq.gz

%.fq.gz: %.fq
	gzip < $< > $@
