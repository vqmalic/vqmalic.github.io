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

> || President || Obama || lives || in || Washington || , || D.C. || at || the || White || House || . ||

This might be perfectly suitable for what you plan to do with the segmented data. However, for many tasks, this somewhat brute-force approach to segmentation simply won't suffice. For example, you might be trying to study co-occurrence patterns in your corpus. In aggregate, you might notice, hm, "white" and "house" occur together but this signal may be overwhelmed by instances where "white" and "house" appear separately (and therefore, in association with other words). The word Washington occurs sometimes with D.C., but in other places in your corpus it refers to the state, or the historical individual, or the university. 

It therefore may be advantageous to merge these phrases together and treat them as a single semantic unit. One way of doing this might look like this:

> || President Obama || lives || in || Washington, D.C. || at || the || White House || . ||

Now, if "white" occurs in conjunction with "house", it will be considered a single semantic unit with its own special properties in whatever subsequent task you apply on the data. The word "white" can occur in isolation, in other contexts, but if it appears in the phrase "white house" then it will retain, together with the word "house", its associations with all things presidential. The same goes with Washington, D.C.. However, as is the case with many other things NLP, there are exceptions. If we group "President Obama" together as a single phrase, then your subsequent algorithm may consider mentions of "President Obama" and "Obama" without the title to be referring to different entities. This _may_ be what you are after; for example, you may be investigating whether or not outlets in opposition to Obama are more likely to refer to him without the title "President." However, outside of specific cases you will most likely want to consider "Obama" to be a standalone semantic unit and then measure its association with words like "President" or "Barack". The final segmentation then may look like this:

> || President || Obama || lives || in || Washington, D.C. || at || the || White House || . || 

##Text Segmentation in Chinese 

The text segmentation task for Chinese is, unsurprisingly, quite different from English language text segmentation. By means of illustration, take the following example sentence.

> 昨天我的電腦突然轉出了一個人的說話的聲音，怎麼回事？
> *Yesterday my computer suddently emitted the sound of some person talking, how did that happen? [^1]*

As with our English example we could just brute force it. But first, notice that there are no spaces in Chinese. We'll have to split by character. 

> || 昨 || 天 || 我 || 的 || 電 || 腦 || 突 || 然 || 轉 || 出 || 了 || 一 || 個 || 人 || 的 || 說 || 話 || 的 || 聲 || 音 || ， || 怎 || 麼 || 回 || 事 || ？ ||
> *past day I (possession) electricity brain suddenly like transfer out one unit person (possession) speak talk (possession) sound sound , how particle return thing ? [^2]*

Obviously this does not work well. In fact, it's worse than our brute force approach to the English sentence, and this is an obvious consequence of some properties unique to Chinese. English has spaces, so the base unit of analysis is the _word_, which we can effortlessly extract from between spaces, and the difficulty comes about in answering the question, which _words_ should we consider together as a single _phrase_? Most words will be considered in isolation so the brute force method gets a good chunk right.

However, in Chinese the base unit of analysis is the _character_ and the question to address is which _characters_ should we consider as a single _word_? Words in Chinese can be anywhere from 1 character in isolation to a combination of up to 3 characters. At a scale much larger than in English, many multi-character Chinese words possess a meaning that is some combination of its constituent characters. A classic Chinese 101 case is the word for computer, 電腦, which consists of the characters for electricity and brain. There are examples of this in English (e.g. "football", "blackboard") but more often than not the constituent meaning of a multisyllabic word is cloaked by its obscure (to most modern English speakers) Greek/Latin/etc. origins (e.g. "psychology"). 

In other cases, the combinatorial element of merging characters to form words is a bit more hazy. Take, for example, the word 聲音, which means "sound". The two characters here on their own _both_ also mean "sound", but in a more general sense. There are other two-character words that draw on one of these characters to incorporate its general "sound" meaning: 聲調 __tone__, 口音 __accent__, 音樂 __music__. However __the__ two-character word 聲音 is the canonical Modern Chinese word for __the__ English word "sound". Chinese-speakers are aware that each constituent character has a "sound-like meaning" but in only special instances would they use 聲 or 音 by themselves in that capacity. 

Thus in Chinese segmentation it is absolutely crucial to identify which characters _should_ go together because it is a step necessary for identifying _words_. In English, we are identifying _phrases_. The importance of this distinction is visible if we compare the relative success of the brute force, split everything method in English and in Chinese. In English, we're losing phrases like "White House" or "Washington, D.C." to noise but the individually extracted words are still meaningful. However, in Chinese, we're losing actual words and the output is an incoherent jumble of characters, many of which, in isolation, carry a only vague, fuzzy meaning. 

Here's a better parsing of the Chinese sentence:

> || 昨天 || 我的 || 電腦 || 突然 || 轉出了 || 一個 || 人的 || 說話的 || 聲音 || ， || 怎麼 || 回事 || ？ ||
> *Yesterday my computer suddenly emitted a person's speech's sound, how happen?*

Much better. In fact, the English translation is a perfect translation when the pecularities of Chinese word order and tense are accounted for. 

I touched on the segmentation problem for Modern Chinese as the problem-solving approaches used here are the natural starting points for doing the same with Classical Chinese. Needless to say, Modern Chinese is a huge language and the problem of Chinese word segmentation (中文分詞) is extremely well researched. Although there still is no solid consensus in _the_ accepted way to parse Modern Chinese[^3] there are excellent tools out there. When working with modern Chinese I use [the segmenter developed by the brains at Stanford](http://nlp.stanford.edu/software/segmenter.shtml) though I've heard high praise of [the Jieba segmenter](https://github.com/fxsjy/jieba) written in Python.

##Text Segmentation in Classical Chinese

When segmenting classical Chinese, there are two questions that we can retain from the discussions above.

1. What is the most effective way to segment the text _in preparation for_ some subsequent machine learning task?
2. What is the most effective way to group characters that _should_ be together, especially characters that in combination constitute a specific word?

I'm specifically interested in segmenting the texts in preparation for [topic modeling with Latent Dirichlet Allocation](http://www.scottbot.net/HIAL/index.html@p=19113.html).

[^1]: Weird sentence, I know, but I wanted a sentence with the word computer in it and this one seemed amusing enough. 
[^2]: My Chinese-speaking colleagues will probably cringe at this, but it's just for explanatory purposes!
[^3]: I'm sure many Chinese-speakers would, in fact, disagree with how I manually parsed the above example sentence.
