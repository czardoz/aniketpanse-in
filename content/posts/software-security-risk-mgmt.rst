Software Security is Risk Management
====================================

:date: 2017-08-30
:tags: software, engineering, security
:bg: /img/bgs/security.png

We should accept the fact that software can *never* be perfect.
Just like any machine cannot ever be built to perfection, software
cannot be built to perfection either. There are attempts to do this,
such as `Formal Verification <https://en.wikipedia.org/wiki/Formal_verification>`_,
but they're not practically deployable at this point. Moreover, they're
usually tailored towards specific use cases, so the entire process would
need to be repeated for every bug fix and feature addition. So it's
tremendously hard (impossible) to formally verify large codebases such
as Chromium, or the Linux kernel.

Turns out, guaranteeing software security is prohibitively costly. For now.
It is however, possible to provide a reasonable level of security by applying
traditional techniques of risk management. Before discussing the techniques
though, it's important to understand *what is the uncertainty here?*. Without
understanding the **uncertainty** in software engineering, we cannot quantify
the risks.

Uncertainty
-----------

In Software Engineering, uncertainty can arise out of any of the following
sources (in the order in which they appear in the `SDLC <https://en.wikipedia.org/wiki/Systems_development_life_cycle>`_):

1. Gathering of requirements
    This is the source of a large amount of uncertainty in many projects. Sometimes,
    requirements are not gathered fully. Or they're wrong (or both!). Other times,
    software meant for a different objective is repurposed for a totally different
    use-case.

2. Architecture
    Architectural flaws in software are extremely dangerous, because they invariably
    tend to be complex and hard to fix. In many cases, the only option is to start
    from scratch, which is extremely costly.

3. Implementation bugs
    As software is created by humans, it's always possible to have flaws during the
    software writing phase.

4. Change
    Many times, new requirements come up, the market changes, or one of the software
    dependencies performs a breaking change. This can create uncertainty in any system.

Assessing the Risk
------------------

Once you identify as many sources of uncertainty as possible, you can proceed to
`assess the risk <https://www.isaca.org/journal/archives/2010/volume-1/pages/performing-a-security-risk-assessment1.aspx>`_.
Going a step further, it might also help to identify sources of uncertainty which
cause a large amount of risk. For example, not having a unit test suite of the software
system causes a huge uncertainty, but if done from the beginning, is not very costly.
Same goes for requirements gathering. If done well at the beginning, it minimizes change,
and reduces the uncertainty (and therefore the risk).

Conclusion
----------

It's important to understand that real-world software is built with a number of contraints,
so it's better to accept their limitations in terms of security. Instead, the right
approach is to embrace the uncertainty, and try to minimize it.
