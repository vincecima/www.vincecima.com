---
title: The Surprising Ease of Plain Text Accounting
layout: post
---

As a long time user of [You Need A Budget](https://www.youneedabudget.com/) I was a little dismayed that the [next version](https://www.youneedabudget.com/blog/post/the-new-ynab-is-here) would be a hosted service. I had tried similar services before ([Mint](https://www.mint.com/), [Buxfer](https://www.buxfer.com/), [PocketSmith](https://www.pocketsmith.com/), etc) and never found the sacrifice of privacy worth it. All of them felt like they were solving problems I didn't have.

With YNAB 4 now unsupported, I started looking for alternatives. Would I finally surrender to Intuit and start using Quicken? Was a Google Docs spreadsheet enough to get me through? I experimented with [GNU Cash](http://www.gnucash.org/) for a bit and found it very powerful but somewhat difficult to use. A search for other open source tools randomly led me to [ledger](http://ledger-cli.org/) and my interest was peaked.

Here was a tool that let me store my data in a plain text file and let me control how and where I wanted to store it. I dove into using it and it made a great first impression. I could open a regular text file and add/edit transactions. It calculated my balance quickly and worked alongside other UNIX tools.

### Transaction Entry

After switching over to ledger as of the first of the month, I quickly found 2 shortcomings. Typing transactions into an editor was easy but repetitive. After some Googling I found an interesting solution. A compatible project called [hledger](http://hledger.org/) existed and offered some user friendly tools beyond what ledger did. Most interesting was a tool called `hledger add`.

<script type="text/javascript" src="https://asciinema.org/a/05doz7160zb7ath81z284bq9e.js" id="asciicast-05doz7160zb7ath81z284bq9e" async></script>

`hledger add` combined the power of pattern matching (for transaction descriptions and accounts) with tab completion to make entering new transactions dead simple.

### Reporting

The second roadblock I hit was reporting on the changes to my net worth from month to month. Doing this with a spreadsheet was tedious but straightforward. After skimming `hledger`'s [manual](http://hledger.org/manual.html), I found the `balance` command. `balance` by default simply reports the current balance for all your accounts.

![hledger balance]({{ site.url }}/resources/hledger-balance-1.png)

On top of that simple report, `hledger balance` also exposes some custom reporting option. I was able to assemble a command `hledger balance ^assets ^liab --depth 2 -M -H` that broke down my net worth monthly by top level account.

![hledger balance ^assets ^liab --depth 2 -M -H]({{ site.url }}/resources/hledger-balance-2.png)

### Conclusion

I have been using `hledger` for 5 months and can't be happier. Its a well supported open source tool that does exactly what I needed while still being adaptable.
