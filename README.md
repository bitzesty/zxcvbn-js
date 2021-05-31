# zxcvbn-js [![Gem Version](https://badge.fury.io/rb/zxcvbn-js.svg)](https://badge.fury.io/rb/zxcvbn-js)

There is now a more compelte zxcvbn implmentation in ruby, so please check out https://github.com/formigarafa/zxcvbn-rb

Fork of [zxcvbn-ruby](https://github.com/envato/zxcvbn-ruby)

This fork runs the JS implementation via ExecJS so that the results are the same. 
This fixes discrepancies in the implentation for using zxcvbn also for client-side validation.

Ruby port of [zxcvbn.js](https://github.com/dropbox/zxcvbn) 

## Usage

Gemfile:

```ruby
gem 'zxcvbn-js', require: 'zxcvbn'
```

Example usage:

```ruby
$ irb
>> require 'zxcvbn'
=> true
>> Zxcvbn.test('@lfred2004', ['alfred'])
=> #<OpenStruct password="@lfred2004", guesses=15000, guesses_log10=4.176091259055681, sequence=[{"pattern"=>"dictionary", "i"=>0, "j"=>5, "token"=>"@lfred", "matched_word"=>"alfred", "rank"=>1, "dictionary_name"=>"user_inputs", "reversed"=>false, "l33t"=>true, "sub"=>{"@"=>"a"}, "sub_display"=>"@ -> a", "base_guesses"=>1, "uppercase_variations"=>1, "l33t_variations"=>2, "guesses"=>50, "guesses_log10"=>1.6989700043360185}, {"pattern"=>"regex", "token"=>"2004", "i"=>6, "j"=>9, "regex_name"=>"recent_year", "regex_match"=>["2004"], "guesses"=>50, "guesses_log10"=>1.6989700043360185}], calc_time=10, crack_times_seconds={"online_throttling_100_per_hour"=>540000, "online_no_throttling_10_per_second"=>150, "offline_slow_hashing_1e4_per_second"=>1.5, "offline_fast_hashing_1e10_per_second"=>1.5e-06}, crack_times_display={"online_throttling_100_per_hour"=>"6 days", "online_no_throttling_10_per_second"=>"3 minutes", "offline_slow_hashing_1e4_per_second"=>"2 seconds", "offline_fast_hashing_1e10_per_second"=>"less than a second"}, score=1, feedback={"warning"=>"", "suggestions"=>["Add another word or two. Uncommon words are better.", "Predictable substitutions like '@' instead of 'a' don't help very much"]}>
>> Zxcvbn.test('asdfghju7654rewq', ['alfred'])
=> #<OpenStruct password="asdfghju7654rewq", guesses=923189026.4430684, guesses_log10=8.965290633567779, sequence=[{"pattern"=>"spatial", "i"=>0, "j"=>15, "token"=>"asdfghju7654rewq", "graph"=>"qwerty", "turns"=>5, "shifted_count"=>0, "guesses"=>923189025.4430684, "guesses_log10"=>8.96529063309735}], calc_time=12, crack_times_seconds={"online_throttling_100_per_hour"=>33234804951.950462, "online_no_throttling_10_per_second"=>9231890.264430683, "offline_slow_hashing_1e4_per_second"=>92318.90264430684, "offline_fast_hashing_1e10_per_second"=>0.09231890264430684}, crack_times_display={"online_throttling_100_per_hour"=>"centuries", "online_no_throttling_10_per_second"=>"3 months", "offline_slow_hashing_1e4_per_second"=>"1 day", "offline_fast_hashing_1e10_per_second"=>"less than a second"}, score=3, feedback={"warning"=>"", "suggestions"=>[]}>
```

## Testing Multiple Passwords

The dictionaries used for password strength testing are loaded each request to `Zxcvbn.test`. If you you'd prefer to persist the dictionaries in memory (approx 20MB RSS) to perform lots of password tests in succession then you can use the `Zxcvbn::Tester` API:

```ruby
$ irb
>> require 'zxcvbn'
=> true
>> tester = Zxcvbn::Tester.new
=> #<Zxcvbn::Tester:0x3fe99d869aa4>
>> tester.test('@lfred2004', ['alfred'])
=> #<Zxcvbn::Score:0x007fd4689c1168 @entropy=29.782, @crack_time=46159.451, @crack_time_display="14 hours", @score=2, @match_sequence=[#<Zxcvbn::Match pattern="spatial", i=0, j=15, token="asdfghju7654rewq", graph="qwerty", turns=5, shifted_count=0, entropy=29.7820508329166>], password"asdfghju7654rewq", calc_time0.00526
>> tester.test('@lfred2004', ['alfred'])
=> #<Zxcvbn::Score:0x007fd4689c1168 @entropy=29.782, @crack_time=46159.451, @crack_time_display="14 hours", @score=2, @match_sequence=[#<Zxcvbn::Match pattern="spatial", i=0, j=15, token="asdfghju7654rewq", graph="qwerty", turns=5, shifted_count=0, entropy=29.7820508329166>], password"asdfghju7654rewq", calc_time0.00526
```

**Note**: Storing the entropy of an encrypted or hashed value provides
information that can make cracking the value orders of magnitude easier for an
attacker. For this reason we advise you not to store the results of
`Zxcvbn::Tester#test`. Further reading: [A Tale of Security Gone Wrong](http://gavinmiller.io/2016/a-tale-of-security-gone-wrong/).
