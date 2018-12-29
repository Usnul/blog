---
date: 2009-12-12T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1261898946"
published: true
unlisted: true
status: publish
tags:
- algorithm
- cards
- sorting
title: Manual Sorting
type: post
url: /2009/12/12/manual-sorting/
---

<div style='text-align:center'>
  <img src="/images/cards.jpg" width="427" height="466" />
</div>

The basis for this post is the following question: <strong>what is the fastest algorithm for sorting a deck of cards by hand?</strong>

<em>TL:DR advisor - skip to the end of the post for a challenge.</em>

In terms of algorithmic complexity, merge sort and quick sort are two of the fastest widely used sorting algorithms - both running at an average case of O(n log n). But if you've ever sorted a deck of cards, ordering primarily by suit and secondly by number, I doubt you used either of these algorithms. You might unknowingly be doing bucket sort, dividing the cards into 4 "buckets" - one for each suit, then sorting each suit using a different algorithm and joining all the suits together at the end.

If I had a deck of cards in my room right now, I would be inclined to take videos of me sorting them using various different algorithms and comparing the time required. I have a feeling that the fastest algorithm would involve drawing out a 4x13 grid on a big piece of paper with each cell labeled with the exact card that fits there, then running through the deck, placing all the cards on their grid cell and just picking them up in order at the end. Of course, this could be accomplished without the grid but requires a spacial sense which I simply do not possess.

As a followup, I have another question: <strong>what if the cards were labeled with a number system you've never seen before?</strong> Assume you have some visual reference allowing you to understand the system, but also assume that the number system is not intuitive. Does your approach change? Of course, the fastest approach here depends on what I'm really asking by "fastest". Given enough time and practice, you would be able to become familiar enough with the numeral system to use any approach you would with regular cards or regular decimal numbers. When I say "fastest", I'm asking how you would minimize the time between receiving the cards and the visual reference and the cards being sorted. In terms of computation, the introduction of this numeral system has drastically increased the time for a comparison or enumeration while not affecting the time for movement or swapping at all. The reverse would be to use the standard deck of cards but make them orders of magnitude larger - say 1 meter wide each. In this case a move or a swap is extremely costly, but a comparison is very cheap.

A more realistic scenario in which the cost of comparison is drastically increased comes about when the criteria used for sorting is not an absolute. Example: sort these pictures by aesthetic appeal. Even by yourself this may be a long process, as you second guess your original decision about the relative appeal of a picture. In the world of web 2.0 though, it's a much much longer process. Sorting by crowd-sourced opinion is the basis of many websites, such as bash.org and reddit. Most of these systems work by providing users with the ability or increase or decrease the item's value by small amounts. Is there some better way of ensuring that the articles which will be the most valuable to the readers will show up at the top?

Returning to the point of manual sorting, I have 2 more things to say.

The first is an idea - competitive sorting. I'm sure this sounds nerdy as hell, but I think I'd still find it fairly entertaining. Groups of people would be given sets of objects and told to sort by some criteria, the fastest group being the victor, with penalties dished out for people falsely claiming their list is sorted. The criteria could be size, weight, volume, buoyancy, color saturation, retail price, alcohol content, power consumption or even something as obscure as average salary of a worker for the company manufacturing the product. Given that I'm at Waterloo, I feel that organizing such a competition isn't all that unlikely. Anyone feel like coming up with a plan for making this actually happen?

The second is a challenge. I challenge everyone reading this to make a video of them sorting a deck of 52 cards (no jokers) as fast as they can, then post the video in the comment section. As soon as I actually get a deck of cards, I'll take part in this challenge myself.

<strong>Rules.</strong>
<ol>
<li> The video must be all in one take</li>
<li> The deck must be shuffled thoroughly on screen, then fanned towards the camera to demonstrate the randomized order of the cards.</li>
<li> Once the cards have been fanned towards the camera, you can have a maximum of 10 seconds of review time looking through the cards before the sorting starts. All cards must stay in contact and in order during this review time.</li>
<li> The timer starts the second a card is separated from the rest of the deck.</li>
<li> The timer stops when the deck has been reassembled into a single sorted deck.</li>
<li> A deck is considered sorted if the cards are primarily ordered by suit in the order diamonds, clubs, hearts, spades, and secondarily ascending numerically.</li>
<li> You must fan the deck towards the camera after it is sorted.</li>
<li> You are allowed any setup you want before starting to sort (e.g. the grid I talked about above,) provided that the setup does not contain moving parts - no robots.</li>
<li> You must be the only one handling the cards - no team efforts.</li>
</ol>

That's all. I fully expect to receive no responses to this challenge, but would love to see the things people come up with if someone does try it.

EDIT: Woohoo! Someone attempted!
<a href='http://www.youtube.com/watch?v=iPDi0QfFvW8' >Timothy Armstrong Sorting</a>

Also, the claimed record according to <a href="http://www.recordholders.org/en/records/cardsorting.html">this page</a> is 36.1 seconds.
