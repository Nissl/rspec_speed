SFRails notes
Brian Moriarty AirBNB
Making your specs run faster
2.5x speedup, not counting app bootup
AirBNB has a maintained Vagrant environment - run by team
In local environment - not continuous integration environment (Solano)
Rspec for all tests (a lot of Capybara, incl. w/JS on - disabled)
Use FactoryGirl - great but slow
Database standard - activerecord model
Don't shard, but have split off some tables - can't join but worth it
Goal change infrastructure, not specs
How to find the slow ones:
  Perftools.rb - sampling profiler, faster than other options (Rubyprof(?) - ))
  Poor man's profiler - debugger/pry - you'll see the slow lines (not on this hackathon)
    - try putitng in spec_helper
Sampled 2 models, 2 controllers
What was slow for them?
  Tune the garbage collector (ruby 2.2 is better)
  There's some gem that controls when runs - every 10 specs default
  1.61x as fast.
Factory
  Make the bare minimum to make valid (already doing this)
  Performance profiler for factorygirl would be cool
  Four saves per user (3x after create blocks)
Optimize IndexEnforcer
  Site w/AirBNB scale - if query w/o index - look out!
  This looks at every query - calls explain - is index used for query?
    Neither explain nor call are fast
  Just put a cache in - have we seen query before - don't run explain, caller
Move user-creation side effects to background job
  Move after_save to after_commit - make sure Resque doesn't beat it there
  And instruct that just on_create
  Except RSpec doesn't commit - it just exits at a save point - so won't fire
  Workaround is a gem, test_after_commit (built it w/Rails 5 (4?))
(Turn on transactional_fixtures - had 18 failing specs -
MySQL couldn't roll back to save points)
Disable SQL time logging to StatsD in Specs
parallel_test - parallel spec - 4 threads on a Macbook pro
upgrade to 2.2
organizer: y@iron.io