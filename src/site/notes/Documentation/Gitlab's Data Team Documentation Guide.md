---
{"created":"2023-01-04T22:30:53 (UTC +01:00)","tags":null,"source":"https://about.gitlab.com/handbook/business-technology/data-team/documentation/#documentation-types","author":"Gitlab Data Team","dg-publish":true,"permalink":"/documentation/gitlab-s-data-team-documentation-guide/","dgPassFrontmatter":true}
---


> ## Excerpt
> This documentation method is not currently used by the data team, but is still a good guide for how to think about the different types of documentation.

---
1.  You are here:
2.  [Business Technology](https://about.gitlab.com/handbook/business-technology/)
3.  [Data Team](https://about.gitlab.com/handbook/business-technology/data-team/)
4.  Data Team Documentation Guide

## On this page

___

This documentation method is not currently used by the data team, but is still a good guide for how to think about the different types of documentation.

## Personas[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#personas)

There is a surfeit of information in the data team handbook. Not everything documented is meant for everyone who might read it. Even within the company we come from different backgrounds and are looking for different pieces of information.

To help readers orient themselves, we've created a series of 4 personas that we believe capture the majority of user types. These are as follows:

### Builder 🛠[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#builder)

Key attributes of a Builder:

-   You have strong SQL abilities for both querying and transforming data
-   Have excellent data fluency
-   Direct access to data (Snowflake Raw data)
-   Strong domain knowledge
-   Contributor / collaborator of documentation and analysis

Typical Roles:

-   Data Engineer
-   Core Data Analyst

### User 💻[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#user)

Key attributes of a User:

-   Low to mid-level SQL abilities for querying data
-   Basic data fluency
-   Direct access to data (Snowflake Raw data)
-   Strong domain knowledge

Typical Roles:

-   Distributed Analyst/Engineer
-   Product Manager
-   Engineering Manager

### Consumer 📊[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#consumer)

Key attributes of a Consumer:

-   Limited to No SQL ability
-   Limited data fluency
-   Strong domain knowledge
-   Limited access to data (Sisense only)

Typical roles:

-   People Manager
-   Business Partner
-   Non-data / technical individual contributor

### Champion 👑[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#champion)

Key attributes of a Champion:

-   Limited SQL ability and limited time
-   Good domain knowledge but rely on direct reports for analytic insights
-   Limited Access to data

Typical roles:

-   Director/VP/CXO
-   Investor

### Everyone 🌎[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#everyone)

This is our catchall grouping in case something is relevant to all personas.

## Documentation Types[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#documentation-types)

The [The Documentation System](https://documentation.divio.com/) details this more than we will here, but the general idea is that there are 4 types of documentation. Each of these are different and are written with different aims in mind. They are:

### Tutorials 🔍[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#tutorials)

A tutorial:

-   is learning-oriented
-   allows the newcomer to get started
-   is a lesson

Analogy: teaching a small child how to cook

### How-to guides 🏁[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#howto)

A how-to guide:

-   is goal-oriented
-   shows how to solve a specific problem
-   is a series of steps

Analogy: a recipe in a cookery book

### Explanation 💡[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#explanation)

An explanation:

-   is understanding-oriented
-   explains
-   provides background and context

Analogy: an article on culinary social history

### Reference 📚[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#reference)

A reference guide:

-   is information-oriented
-   describes the machinery
-   is accurate and complete

Analogy: a reference encyclopaedia article

## Usage[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#usage)

You'll notice there are emojis next to both the persona and documentation type headers. We use the emojis in the rest of the data team portion of the handbook to help readers quickly understand the audience and purpose of different sections. For example:

-   🛠🏁 Indicates this is a how-to guide probably for somebody on the data team. An example would be how to provision somebody in Snowflake
-   💻📚 Indicates this is a reference for our user persona. An example would be the tips and tricks section for working in Sisense
-   🌎💡 Indicates this is an explanation for all personas. An example would be the charter of the data team.

Note that the emojis will not be inclusive of everyone who may in fact be interested in the section. Our aim is only to provide a quick guide to help orient the reader for what they're reading.

## Contribution to the Data Team Handbook[](https://about.gitlab.com/handbook/business-technology/data-team/documentation/#contribution-to-the-data-team-handbook)

Every team member on the Data Team is encouraged to contribute to the handbook. Everybody in the `@data-team` is a **[code owner](https://gitlab.com/gitlab-com/www-gitlab-com/-/blob/master/.gitlab/CODEOWNERS)** of our Data Team Handbook pages, but with duties:

1.  All handbook changes are peer reviewed and not merged before a peer has provided their approval.
2.  All MRs for handbook changes have an appropriate name which is important for tracking changes via the [Handbook Changelog](https://about.gitlab.com/handbook/CHANGELOG.html)). Please indicate that it's a Data Team handbook change.
3.  Tag applicable team member(s) that the MR would directly impact and allow 24-48 hours for the team members to review. It is a good practice to have even the smallest changes to the handbook reviewed by at least one team member.
4.  Feel free to discuss/demo your handbook contribution during the Data Team demo meeting.
5.  All MR's that contribute to the Data Team Handbook should be assigned the ~"BT Data Team" label.
