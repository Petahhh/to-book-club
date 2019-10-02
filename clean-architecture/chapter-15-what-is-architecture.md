# Chapter 15 - What is Architecture

The job of the Architect is to maximize productivity and leave as many options open for as long as possible and minimize the lifetime cost of the system. Good architects make it so that the system is easy to understand, develop, maintain, and deploy.

## Architects must work with code

In the best case scenario, architects are programmers, not just conceptual thinkers. Essentially, it's impossible to do the job properly if an architect isn't experiencing the same sort of problems they’re creating solutions for. 

## An architect has to balance development, deployment, maintenance, and operation.

The harder to develop the system is, the shorter its life expectancy. The higher the cost of deployment, the less useful the system is. A system should ideally be deployable with a single action.

Architectures that impede dev or deployment are more costly than those that impede operations or maintenance.

## Architects maintain flexibility

Software has two types of value: the value of its behaviour and the value of its structure.

> The second of these is the greater of the two because it is this value that makes software *soft*.

Since software's essential purpose is to quickly change the behavior of machines to suit a complex landscape of evolving needs, we need it to be malleable. Good software architecture should leave as many options open for as long as possible in order to maintain this flexibility.

## Policy and Details

Software systems contain two major components: policy and details.

Policy - all the business rules and procedures - is the most valuable part of the system. A good architect creates a system where details are not going to interfere with policy; essentially, details are made irrelevant to the policy. As a result of this practice, decisions about details can and should be deferred so that policy comes first.

A good architect maximizes the decisions which are *not* made. High-level policy should be developed against the interface to the outside world, and should not care about things like dependency resolution.

The details are those things that are necessary to enable humans, other systems, and programmers to communicate with the policy, but that do not impact the behavior of the policy at all. They include IO devices, databases, web systems, servers, frameworks, communication protocols, and so forth.

The longer you wait the more info you have. Good architects carefully separate details from policy and then decouple policy from the details. This means the project doesn't need to commit to decisions too early on, and the system stays as flexible as possible as a result.

