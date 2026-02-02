# Why In-House Education Matters Now

## Disclaimer

This post does not argue that companies are solely responsible for developer education. It argues that modest, intentional support for learning creates measurable returns.

---

## The Benefits of a Strong Developer Culture

Having a strong developer culture, where people are genuinely excited and curious about new solutions and technologies, is a real asset for any software organization. It cultivates several important benefits:

- It helps ensure that developers stay up to date with modern tools, frameworks, and approaches. This gives them the ability to recognize and apply the most suitable solution when it fits a forthcoming task, rather than defaulting to legacy technologies.

- It keeps developers engaged by building novelty and exploration into their everyday work. This does not only improve job satisfaction, but also has a direct positive impact on performance. Curious and motivated developers tend to think more critically, experiment more confidently, and deliver higher-quality solutions.

- It fosters a mindset of continuous learning, where improvement is seen as a natural part of the job rather than an optional extra. Over time, this leads to stronger teams, better collaboration, and a higher overall technical standard within the organization.

---

## The Cost of Stagnation

The absence of a continuous learning culture has consequences, even if they are not immediately visible. Over time, teams tend to default to familiar tools and patterns, not because they are the best fit, but because they are the safest known option.

A concrete example is the continued use of raw owning pointers and manual new/delete in modern C++ code, even where ownership semantics could be made explicit and safer through smart pointers or value types.

Perhaps most importantly, stagnation compounds. As the gap between current best practices and everyday engineering grows, teams become less confident experimenting with new approaches. Decisions become increasingly conservative, adaptation slows, and new challenges are solved with familiar tools rather than appropriate ones.

The obvious question, then, is how to implement such a culture without incurring major costs. Sending all developers to external courses every few months is rarely realistic, both in terms of finances and time away from productive work. Fortunately, continuous learning does not have to be expensive.

---

## A Practical, Low-Cost Solution

One effective and low-cost approach is to organize a monthly tech talk session with a follow-up discussion, centered around high-quality external content such as well-selected technical talks from YouTube (channels like CppCon, PyCon, and many others) or other reputable platforms. These videos serve as the primary learning material and are chosen by senior developers or tech leads to align with current or upcoming engineering challenges.

So if your developers keep introducing new singletons, that might be a signal — not for stricter code reviews, but for shared learning and discussion around alternative designs that better fit your codebase.

Supporting continuous learning is a low-cost way for organizations to protect engineering quality while maintaining the ability to adapt to new technical challenges.
