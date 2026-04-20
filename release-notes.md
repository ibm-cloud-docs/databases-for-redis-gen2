---

copyright:
  years: 2026
lastupdated: "2026-04-20"

keywords:

subcollection: databases-for-redis-gen2

content-type: release-note

---



{{site.data.keyword.attribute-definition-list}}



# Release notes for _product-name_
{: #my-service-relnotes}



Use these release notes to learn about the latest updates to _product-name_ that are grouped by _date or build number_. Release notes are available for a minimum of three years.
{: shortdesc}


For information about changes to the _product-name_ API, see [Change log for _product-name_ API](/docs/link-to-change-log).
{: tip}

## How should I set up my page?
{: #relnotes-page-setup}
{: release-note}

* Use "Release notes for xxx" as your title, where xxx is the short name with no trademarks.
* Name the file `release-notes.md` for URL readability.
* If you require multiple release notes files, group under a "Release Notes" topicgroup and use a unique name for each file.
* Add each release as an H2 or H3, depending on how frequently your service releases updates. If you release monthly or less, use an H2 for each entry. If you release several times a month, use an H2 with the month to group each H3 entry in that month.
* The first entry in your release notes file should introduce your service and reflect the release date of the service.
* Use a definition list entry for each update, change, or new item in that release.
* Set the `release-note` content type attribute definition at the top of your file.
* Set the `release-note` content type attribute on a new line following each H2 release entry.
* Do not repeat task steps. Summarize and link off to task topic.
* Do not include security bulletins or maintenance notifications in this file. There is a separate process for these types of notifications.

## What should I include in my release note entries?
{: #release-notes-content-include}
{: release-note}

Use a definition list to highlight each item covered in the release. Each entry should summarize the release details. You want to make sure you are not re-documenting information that is already available in documentation because then you'd have to maintain it in two places. If a more detailed explanation for the change exists out in a documentation page, then link out to the doc. For guidance on coding definition lists, [Definition lists](https://cloud.ibm.com/docs-internal/writing?topic=writing-lists#definition-lists).

Because this content is single-sourced and pulled into the Status UI, you can only include the following markup in your definition list entries: paragraph, ordered list item, unordered list item, code phrase, links, keyrefs, bold, and italics. Any other markup is not supported.

For detailed guidance on what to include on this page, see [Release notes guidance](https://cloud.ibm.com/docs-internal/writing?topic=writing-releasenotes).

## 1 September 2021
{: #subcollection-date-for-update}
{: release-note}

Item 1
:   The classic toolkit is shut down as of 7 August 2020 and is replaced by Watson Studio. You can migrate the training data for classifiers created outside of Watson Studio until 30 September 2020. After you migrate, you can easily update the training data and create another classifier within Watson Studio.

Item 2
:   You can now create Key Protect resources in the US East region.

## 1 August 2021
{: #subcollection-date-for-update}
{: release-note}

Single release item title
:   Single release item description.

## July 2021
{: #subcollection-jul21}

### 27 July 2021
{: #subcollection-jul2721}
{: release-note}

New! IAM trusted profile support
:   Link your cluster to a trusted profile in IAM so that the pods in your cluster can authenticate with IAM to use other {{site.data.keyword.cloud_notm}} services.

Master versions
:   Master fix pack update changelog documentation is available for Kubernetes version [1.21.3_1525](/docs/containers), [1.20.9_1547](/docs/containers), [1.19.13_1554](/docs/containers), and [1.18.20_1559](/docs/containers)

### 26 July 2021
{: #subcollection-jul2621}
{: release-note}

Secrets management
:   For centralized management of all your secrets across clusters and injection at application runtime, try [{{site.data.keyword.secrets-manager_full_notm}}](/docs/secrets-manager?topic=secrets-manager-tutorial-kubernetes-secrets).

{{site.data.keyword.block_storage_is_short}} add-on
:   [Version `3.0.1`](/docs/containers) of the {{site.data.keyword.block_storage_is_short}} add-on is available.

## 1 June 2021
{: #subcollection-jun0121}
{: release-note}

Introducing _product-name_
:   Description of your service.
