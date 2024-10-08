## On the topic of deploying a multitude of system agents.

This post is based off a thread in the [macadmins slack](https://www.macadmins.org) that it appears no-one has blogged about yet. A heartfelt thanks for those that contributed ideas.

![](https://breardon.home.blog/wp-content/uploads/2022/05/screen-shot-2022-05-19-at-11.24.50-am.png?w=1024)

As admins we are often asked to deploy a variety of agents or system extensions onto the computers and devices we administer. These can make sense and often required, for example an inventory service or to meet some security requirement. But sometimes we're asked to deploy software that has conflicts or duplicated functionality with existing agents with little argument to prevent or at least have a discussion about the reasons or purpose. The classic case is being required to install two or more security products, each of which at best overlap in a large percentage of function or at worst, conflict and fight against each other in a battle of dominance over the device, reducing performance, battery life and the user experience.

As admins, our job is to protect the production environment and user experience of the systems we manage. What follows is a compiled list (in no particular order) of questions and expectations from a number of authors in the macadmin community that could be used the next time some team or higher up wants you to deploy the Next Great Thing™ they saw on an airport banner ad or product brochure.

## **Considerations before deploy**ment

What's the plan for ensuring that the latest version of this is always available quickly for deployment?

- Access to vendor download sites or location where the latest supported version is available for packaging

Can other agents be removed due to duplicated functionality as a result of installing this?

Who is responsible for testing?

- Ideally the owner of the agent should be involved in testing directly new versions on the latest OSes before rollout to the entire fleet.

For agent related issues, which team is responsible for actioning incidents and requests?

- What resources are being provided to support the product.

What's the impact to end user experience with having this installed?

- What user impact testing has been performed? Has anyone run this for an appropriate amount of time on a daily workstation?

What configurations and exceptions have you come up with to ensure that performance sensitive users are not adversely impacted by its installation?

What features does this agent bring that we do not already have on our endpoints?

Has testing already been done with this agent in combination with all the other ones we have installed?

- What was the result of that testing and who was involved?
- Who in the requesting team is running macOS, Linux or Windows as their primary desktop and is going to be in the canary group?

What compliance requirement are being met by the agent?

- Provide documentation

What's the history of this product when it comes to timely OS compatibility updates?

- Does the vendor respond quickly to OS releases or do they lag? By how long, days months years? To they build and test against beta OS releases? 

What technical capability does it add that nothing else does?

What organisational bottleneck does this attempt to work around?

Full configuration information for Group Policy/MDM profiles will need to be provided so that no manual actions are required on the part of users for installation or updates.

## **Expectations after deployment**

If this breaks critical workflows during a crucial time, what's the process for approval to remove this from devices?

- Approval or justification may be sought **after** agent removal.
- What's the plan for rolling back when this is panicking/crashing machines?

The team owning this new agent understands that if there is a new OS update and the vendor states that a newer minimum version is required for compatibility for this version of the OS that there is an expectation that this agent (and any associated infrastructure updates) will need to happen in a timely fashion and not be a blocker for rolling out new OS versions?

It is understood that if the agent prevents installation of a new OS update which has high enough severity CVEs for too long, due to -other- compliance requirements for OS updates in a timely fashion, that this product will be removed until it's compatible again with the newer OS version

- It is considered more important that CVEs get patched than having this new agent installed.
