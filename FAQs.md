**Q.** I may not know all of my dependencies at the time I need my first loaded. Can I call $.depends as many times as I wish?
  * **A.** New dependencies can be added at any time.
**Q.** I use a very rich client (rare full page requests) so my loading logic may change over time, can I change my options after I've already configured them?
  * **A.** Yes, options can be modified at any time (adding/removing event handlers, etc)
**Q.** Are url's case sensative?
  * **A.** Short answer, no. However, even though we preserve the case of all urls (and all HTTP requests use this exact case), for sake of ease of implementation urls are indexed in lowercase to help alleviate collisions/duplications.
**Q.** Are there any capacity limitations?
  * **A.** There are no hardcoded limitations.
**Q.** Wouldn't it be wise to make a "loader" plugin not dependent on any other frameworks, like jQuery?
  * **A.** There are benefits to doing so, but the sacrafice is made because 1) Promoting the use of jQuery, and 2) Leveraging lessons learned instead of reinventing the wheel (though some could argue this library contradicts that statement -- I disagree).