---
author: "Zulko"
title: "DNA construction strategies"
date: 2021-04-08T08:47:11+01:00
tags: [synbio, DNA, assembly, software]
draft: true
---

> _This is a bloggification of [these IWBDA 2019 slides]() on how path-finding tricks used in Google Maps and real-time strategy games can also help build long DNA molecules._

In my previous job at the Edinburgh Genome Foundry, customers would email us long DNA sequences (typically 10,000 or more nucleotides ATGC...) which we assembled from smaller DNA fragments with our robots:

<img
  src="../../post_assets/dnaweaver/foundry.jpg"
  style='display: block; margin: 1.5cm auto 1.5cm;'
  width=550
/>

This platform can assemble thousands of DNA fragments per day, but how do you find enough customers to feed the beast? How do you enable biologists worldwide to order DNA constructs as easily as you'd book a plane ticket?

One of our first projects, a collaboration with Autodesk, was a customer portal where users could design DNA sequences and order them from froundries:

<img
  src="../../post_assets/dnaweaver/design_order.png"
  style='display: block; margin: 1.5cm auto 1.5cm;'
  width=500
/>

The website was sunset before it could become the Skyscanner of large DNA, but we kept working on the quote generation part -- how to automatically analyse a customer's DNA sequence and determine the cheapest, fastest way that our foundry could build it. This is a problem every DNA provider has, but it becomes particularly difficult for longer sequences, which can be built in many, many ways.

## From parts, from scratch, from a mix-and-match

How you build long DNA sequences depends on your preferred DNA assembly techniques, your favorite DNA fragments providers, what DNA fragments you already have in stock, and how cheap and fast you want the whole project to be.

### Parts assembly projects

You can build a DNA construct by fusing together standard genetic parts that you may already have in the fridge. The construct can then serve as a part for the next, higher order construct, and so on until you obtain the desired assembly:

<img
  src="../../post_assets/dnaweaver/basic_parts_assembly.png"
  style='display: block; margin: 0.5cm auto 0.5cm;'
  width=350
/>

This is cheap (since you already own the DNA building blocks, and they're easy to photocopy), fast (around one day per assembly, since the parts are designed to assemble easily), and the planning consists mainly in finding the best order in which to assemble the parts, possibly with the help of software like RavenCAD ():

<img
  src="../../post_assets/dnaweaver/ravencad.png"
  style='display: block; margin: 1cm auto 1cm;'
  width=650
/>

### Synthetis from scratch

Instead of relying on existing DNA parts, you can build completely new sequences from the bottom up. You start by synthesizing small _oligo_ DNA molecules of 40-100 nucleotides, one A,T,G,C at a time. From these oligos you assemble bigger fragments (1000 nucleotides), then fuse these fragments into even bigger blocks etc.

<img
  src="../../post_assets/dnaweaver/basic_from_scratch.png"
  style='display: block; margin: 1cm auto 1cm;'
  width=400
/>

Whole genomes of million of nucleotides have been built this way. Each genome synthesis project generally has its own variations, and implements custom software to plan the thousands of assembly operations required (which are then performed by automated biofoundries or by armies of students and postdocs).

Here is a fully synthetic and re-designed XXX genome. Note that they didn't start from printed oligos, and instead ordered 1,000-nucleotide blocks from a company specialized in DNA printing:

<img
  src="../../post_assets/dnaweaver/genome_caligrapher.png"
  style='display: block; margin: 0 auto 1cm;'
  width=450
/>

### Bit-of-both

Our customer's projects were rarely built entirely from parts or scratch, however, and would rather be a mix of DNA reuse and de-novo synthesis.

A typical example is the assembly of pre-existing genetic parts from our standard library with a gene that would be synthesized by one of our external vendor:

<img
  src="../../post_assets/dnaweaver/library_and_vendors_mix.png"
  style='display: block; margin: 1.5cm auto 1.5cm;'
  width=500
/>

Some vendors are cheap but will only accept the "easy" sequences (count 100$ for a 1kb gene), some are more expensive but will really try and synthesize anything. All vendors have limits on the sequence size, and when the gene exceeds these limits we would order it in multiple fragments (sometimes from different providers) that would then be fused together during the assembly operation:

<img
  src="../../post_assets/dnaweaver/library_and_vendors_mix_fragments.png"
  style='display: block; margin: 1.5cm auto 1.5cm;'
  width=500
/>

It's starting to look like an interesting computational problem! Let's throw in one more popular cloning technique: the PCR extraction. If the gene you want is from an organism you have in your freezer (for instance _E. coli_ bacteria), you can easily extract the gene by ordering two small oligos that match its starting and ending sequences, and will guide an enzyme to create copies of it from the organism's chromosome:

<img
  src="../../post_assets/dnaweaver/basic_pcr.png"
  style='display: block; margin: 1.5cm auto 1.5cm;'
  width=580
/>

This method can get you 4,000-nucleotide genetic components for just a few dollars and a day of work! But what if the sequence naturally present is E. coli is almost the sequence you want, but with two little differences? In that case you would use a variant (called _site-directed mutagenesis_) in which you order 6 oligos, create 3 fragments of the gene with slighlty altered ending sequences, and fuse everything together:

<img
  src="../../post_assets/dnaweaver/basic_site_directed_mutagenesis.png"
  style='display: block; margin: 0 auto;'
  width=630
/>

### Selecting a stitch

Not only can DNA fragments come from many different sources, they can also be assembled in many ways, each with different sequence constraints:

Gibson: triangle exclamation mark: mutation-prone (uses a polymerase) triangle mutation prone as DNA gets re-polymer

Golden Gate Assembly: triangle exlamation: sequence should not contain "CGTCTC" works very well but requires the sequence to be free on some sequences, such as "CGTCTC". Gibson Assembly can be used assemble most sequences but is sensitive to repeated patterns and prones to mutations. LCR assembly isn't prone to mutations and doesn't have any forbidden sequence, but is prone

Depending on your lab, the type of sequence, etc. the assembly method, and therefore the sequences you'll order, will vary.

### So yeah, it's complicated

The multitude of sources and assembly methods can make the planning of large DNA assemblies a hard combinatorial problem. For the most complex projects, some labs resort to planning councils, gathering all team members with DNA wisdom in a same room, putting the sequence on display, and getting everyone's input:

<img
  src="../../post_assets/dnaweaver/elrond.jpg"
  style='display: block; margin: 2cm auto 2cm;'
  width=450
  title="I can't meme"
/>

Even with some many scholars involved, errors can be made, better strategies can be missed. So how do you replace this meeting by a piece of software?

## Step 1: representing the problem

"I assemble sequences using Golden Gate assembly and I buy fragments from one of these vendors"

<img
  src="../../post_assets/dnaweaver/example_supply_network.png"
  style='display: block; margin: 1cm auto 2cm;'
  width=350
/>

This variation means "I buy fragments from 3 possible vendors, but for a given sequence I want all fragments to come from the same vendor".

This one means "I assemble oligos into blocks, blocks into bigger blocks, etc."

And this one:



Here is one supply network that can be used for PCR extraction and and site-directed mutagenesis.

I don't want to bore anyone with the details of how each station answers a request, but I spent a lot of time doing all these drawings so here they are:

<img
  src="../../post_assets/dnaweaver/sources/parts_library.png"
  style='display: block; margin: 1cm auto 2cm;'
  width=500
/>
<img
  src="../../post_assets/dnaweaver/sources/dna_vendor.png"
  style='display: block; margin: 1cm auto 2cm;'
  width=450
/>
<img
  src="../../post_assets/dnaweaver/sources/PCR_extraction.png"
  style='display: block; margin: 1cm auto 2cm;'
  width=450
/>
<img
  src="../../post_assets/dnaweaver/sources/assembly_station.png"
  style='display: block; margin: 1cm auto 2cm;'
  width=450
/>
<img
  src="../../post_assets/dnaweaver/sources/source_comparator.png"
  style='display: block; margin: 1cm auto 2cm;'
  width=450
/>

These blocks have pretty simple logics, with the notable exception of the assembly station, which has a central responsibility: finding the optimal decomposition of a DNA sequence into fragments that are easy to obtain and assemble together. And that's an interesting problem.

## Step 2: finding the right cuts




This is probably one of the favorite computational tricks I've ever pulled in that field. As much as I would want to be the first and only and finally put my name on a method and make mom proud, it seems that other devs have independently reached the same method. [Twist Bioscience]() (a major DNA provider) has been recruiting developers with [graph theory skills](https://www.smartrecruiters.com/TwistBioscience/84826182-python-software-engineer-algorithm). Doulix, a Venice-based DNA manufacturer with one of the best software fronts in Europe, also tweeted this, which looks like graphs and paths and assembly optimization:
https://twitter.com/Doulix_SynthBio/status/938068271986823168

The interesting thing with the graph-based method is how flexible it is

### Cloning constraints as graph operations


- _"My assembly method doesn't work with fragments longer than X or shorter than Y"_: just remove all edges in the graph which are longer than X and shorter than Y.
- _"This assembly method works badly when the fragment fusion regions contain very low GC or repeated sequences"_: remove the graph nodes corresponding to such regions.
- _"The assembly method works badly when assembly more than 10 parts"_: put a constant penalty on each edge to reduce the number of edges in the shortest path.
_"Golden Gate assembly overhangs should all be inter-compatible"_ (read this other article on the subject): If the assembly corresponding to the shortest path has imcompatible overhangs, use the XXX algorithm to iterate through the 2nd-shortest-path, 3rd, 4th, etc. until one assembly has compatible overhangs. 
- _"Oligo assembly only works with an even number of fragments"_ I actually don't have an elegant solution for this one, but [here is a stackoverflow suggestion](https://stackoverflow.com/questions/32722448/shortest-path-with-even-number-of-edges) for finding even-edged shortest path via an heavy transformation of the graph.

### A* Acceleration 

The Dijkstra shortest-path algorithm is great, but may take a few minutes or even an hour to decompose the longest sequences, because of all the edges to evaluate. This makes it good enough for a foundry's planning, but too slow to give your customers the flight-booking experience.

Finding a route on a flight-booking website, like finding an itinerary for a Google Maps user or an Age of Empires army, is the kind of path finding problem where a solution but be found quickly, and it's fine to miss the absolute best route from time to time. These problems use the A* algorithm, a path-finding refinement that gets fed an additional magic piece of imformation that it will use to cut down some of the exploration.

Here is an illustration of the difference, from the [A\* wikipedia page](https://en.wikipedia.org/wiki/A*_search_algorithm) (courtesy of user [Subh83](https://en.wikipedia.org/wiki/User:Subh83?rdfrom=commons:User:Subh83)).

<table>
  <tr>
    <th>Dijsktra algorithm</th>
    <th>A* algorithm<br/>50% space exploration, +10% longer path</th>
  </tr>
  <tr>
    <td width:50%>
      <img src="https://upload.wikimedia.org/wikipedia/commons/5/5d/Astar_progress_animation.gif"
      style="border: 1px solid #ddd"
      />
    </td>
    <td>
      <img src="https://upload.wikimedia.org/wikipedia/commons/8/85/Weighted_A_star_with_eps_5.gif"
      style="border: 1px solid #ddd" />
    </td>
  </tr>
</table>

In a Google Maps search, the magic piece of information could be the average speed that the public transportation allows. In the special case of DNA sequence decomposition for assembly, that would be how the averag DNA source charges per basepair. If you get it too low, the search ressembles the Djikstra search (no speed gain), if you get it too high, every solution will appear cheap, the search will be rushed and naive. If you get it just right, you get quick and accurate assembly plans. The algorithm will understand that Parts Libraries and PCR extraction stations are like low-cost companies (they can get you far for cheap, as long as you agree with the take off and landing sites), oligo assembly is like walking (not cheap, not fast, but sometimes your only option), expensive companies are like taxicabs,  etc.
By combining the A* algorithm 



# Conclusion

Looking back at this giant wall of text, it is pretty clear that:

- I can't meme
- Decision-automating software is cool and every foundry should have as much as possible (and I'm not saying that as a foundry software person).
- Part libraries are the Ryan Air of DNA construction.