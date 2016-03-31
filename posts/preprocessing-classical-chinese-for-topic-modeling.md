<!-- 
.. title: Preprocessing Classical Chinese for Topic Modeling
.. slug: preprocessing-classical-chinese-for-topic-modeling
.. date: 2016-03-31 01:45:46 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

##Text Segmentation

Text segmentation is a common task in natural language processing where the goal is to take a text and divide it into "meaningful units". This is usually done as a preprocessing step for a more intricate task like text classification or topic modeling. The subsequent task of interest is usually what dictates what is meant by "meaningful units." Take, for example, the following sentence.

> President Obama lives in Washington, D.C. at the White House.

<!-- TEASER_END -->

Perhaps the simplest way to segment this sentence is just to isolate every single word.

> ++ President ++ Obama ++ lives ++ in ++ Washington ++ , ++ D.C. ++ at ++ the ++ White ++ House ++ . ++

This might be perfectly suitable for what you plan to do with the segmented data. However, for many tasks, this somewhat brute-force approach to segmentation simply won't suffice. For example, you might be trying to study co-occurrence patterns in your corpus. In aggregate, you might notice, hm, "white" and "house" occur together but this signal may be overwhelmed by instances where "white" and "house" appear separately (and therefore, in association with other words). The word Washington occurs sometimes with D.C., but in other places in your corpus it refers to the state, or the historical individual, or the university. 

It therefore may be advantageous to merge these phrases together and treat them as a single semantic unit. One way of doing this might look like this:

> ++ President Obama ++ lives ++ in ++ Washington, D.C. ++ at ++ the ++ White House ++ . ++

Now, if "white" occurs in conjunction with "house", it will be considered a single semantic unit with its own special properties in whatever subsequent task you apply on the data. The word "white" can occur in isolation, in other contexts, but if it appears in the phrase "white house" then it will retain, together with the word "house", its associations with all things presidential. The same goes with Washington, D.C.. However, as is the case with many other things NLP, there are exceptions. If we group "President Obama" together as a single phrase, then your subsequent algorithm may consider mentions of "President Obama" and "Obama" without the title to be referring to different entities. This _may_ be what you are after; for example, you may be investigating whether or not outlets in opposition to Obama are more likely to refer to him without the title "President." However, outside of specific cases you will most likely want to consider "Obama" to be a standalone semantic unit and then measure its association with words like "President" or "Barack". The final segmentation then may look like this:

> ++ President ++ Obama ++ lives ++ in ++ Washington, D.C. ++ at ++ the ++ White House ++ . ++ 

##Text Segmentation in Chinese 

The text segmentation task for Chinese is, unsurprisingly, quite different from English language text segmentation. By means of illustration, take the following example sentence.

> 昨天我的電腦壞了，不知道怎麼辦。
> *Yesterday my computer broke, I don't know what to do about it.* 