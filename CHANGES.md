# CHANGES

## rest-graph 2.0.3 -- 2013-04-26

* [RestGraph::RailsUtil] Adopt latest changes from rest-more. See:
  https://github.com/cardinalblue/rest-more/commit/101e4b25f11e4cf3713fa2f1d6b5a46982266e44
  https://github.com/cardinalblue/rest-more/commit/0e7d4db588ecc287fd8b2840c880dfb3fe3c3096

## rest-graph 2.0.2 -- 2012-07-13

* [RestGraph] Adopt latest em-http-request.

### Bugs fixes back ported from [rest-more][]

* [RestGraph::RailsUtil] Change the redirect log level from WARN to INFO.
* [RestGraph::RailsUtil] Since Facebook would return correct URL now,
  we don't have to try to use `URI.encode` anymore. Actually, that
  causes bugs.

Please upgrade to [rest-more][].

## rest-graph 2.0.1 -- 2011-11-25

### Bugs fixes back ported from [rest-more][]

* [RestGraph] Now we're using POST in `authorize!` to exchange the
  access_token with the code instead of GET. If we're using GET,
  we would run into a risk where a user might use the code to
  get other people's access_token via the cache. Using POST would
  prevent this because POSTs are not cached.

* [RestGraph::RailsUtil] Fixed a serious bug. The bug would jump up if
  you're using :write_session or :write_cookies or :write_handler along
  with :auto_authorize, for example:
  `rest_graph_setup(:auto_authorize => true, :write_session => true)`
  The problem is that RestGraph::RailsUtil is not removing the invalid
  access_token stored in session or cookie, and yet it is considered
  authorized, making redirecting to Facebook and redirecting back doesn't
  update the access_token. `rest_graph_cleanup` is introduced to remove
  all invalid access_tokens, which would get called once the user is
  redirected to Facebook, fixing this bug.

[rest-more]: https://github.com/cardinalblue/rest-more

## rest-graph 2.0.0 -- 2011-10-08

We have moved the development from rest-graph to [rest-core][].
By now on, we would only fix bugs in rest-graph rather than adding
features, and we would only backport important changes from rest-core
once in a period. If you want the latest goodies, please see [rest-core][]
Otherwise, you can stay with rest-graph with bugs fixes.

[rest-core]: https://github.com/cardinalblue/rest-core

* [RestGraph] Added `RestGraph#parse_fbsr!` which can parse Facebook's new
  cookie. Also, `RestGraph#parse_cookies!` would look for that too.
* [RestGraph] Added `expires_in` attribute which would be passed to cache.
  The default value is 600 seconds. No effect if there's no cache, or if
  the cache didn't support `:expires_in` option.
* [RestGraph] Now `RestGraph#initialize` accepts string keys.
* [RestGraph] We don't support em-http-request >= 1 at the moment.
* [RestGraph] Fixed that parsing an invalid signed_request would raise an
  error. From now on it would simply ignore it and wipe out the data.

* [RailsUtil] Now by default, RestGraph would cache all GET requests in
  `Rails.cache` for 600 seconds. You can change this by running:

      rest_graph_setup(:cache => nil, :expires_in => 3600)

  To disable cache or lengthen/shorten the lifetime of the cache result.

## rest-graph 1.9.1 -- 2011-06-07

* [RestGraph] Fixed parse_fbs! where fbs contains some json. (thanks Bruce)

## rest-graph 1.9.0 -- 2011-05-26

* [RestGraph] Removed deprecated rest-graph/auto_load.rb, and
              rest-graph/autoload.rb. Please simply require 'rest-graph'

* [RestGraph] Removed deprecated strict option. Facebook had fixed their bug.

* [RestGraph] Removed deprecated broken_old_rest. Please use old_rest instead.

* [RestGraph] Removed deprecated suppress_decode. Plz use auto_decode instead.

* [RestGraph] Introduced RestGraph#attributes.

* [RestGraph] RestGraph#lighten and RestGraph#lighten! now takes an extra
              argument which let you set attributes.

* [RestGraph] Now RestGraph#get/post has an option for timeout which
              make this request use this timeout instead of the default one.

* [RestGraph] URI.encode on Facebook's broken paging URL. (thanks andy)

* [RestGraph] Now all old_rest based methods accept a :post option, which
              means use POST for this API request, but still treating it
              a GET request for rest-graph, so that caching and other
              similar features would still work.

              The reason for this option is that some FQL could be very long,
              FQL multiquery could be even much more longer! Long URLs might
              cause problems, thus we have provided this option to use POST
              instead.

## rest-graph 1.8.0 -- 2011-03-08

*  [RestGraph] Now require 'rest-graph/autoload' is deprecated, simply use
               require 'rest-graph' would require anything you "might" or
               might not want. Use require 'rest-graph/core' for core
               functionality instead.

*  [RestGraph] Now RestGraph#get/post has two extra cache options, one is
               `expires_in', to indicate how long does this result should be
               cached. Another one is `cache', if passing false, it means
               the cache should be updated with this request, no matter it's
               cached or not before.

* [ConfigUtil] LoadConfig is renamed to ConfigUtil, and autoload is removed.

* [ConfigUtil] ConfigUtil is extended into RestGraph, so RestGraph.load_config
               is equivalent to ConfigUtil.load_config.

*  [RailsUtil] Now FBML canvas is no longer supported. Setting iframe in
               rest-graph.yaml or pass to rest_graph_setup(:iframe => true)
               would be a no-op. Setting :canvas => 'name' should imply we're
               using iframe. This make it less trouble to find how to do the
               redirect correctly.

*  [RailsUtil] Now it should work better with Rails3. Previously, the load
               order problem would make rest-graph.yaml is not auto-picked.
               For now we're using Railtie initializer to make it load better.
               Rails2 should not be affected with this change.

*  [RailsUtil] Now rest-graph related methods are all private or protected,
               this would avoid them being treated as Rails actions.

*  [RailsUtil] Fixed a bug that calling rest_graph_setup didn't update options
               for rest_graph. This is fixed by reinitialize rest_graph in
               rest_graph_setup.

*  [RailsUtil] You can use rest_graph_js_redirect to do full page redirect
               in canvas iframe.

*   [TestUtil] Fixed stub format for method/fql.multiquery. Facebook has weird
               and inconsistent format for different API.

* [FacebookUtil] Added some very Facebook specific utilities.

## rest-graph 1.7.0 -- 2010-12-30

* [RestGraph] Renamed rest-graph/auto_load to rest-graph/autoload; auto_load
              would be still working for a while.

* [RestGraph] Renamed suppress_decode to auto_decode, and reverse semantics.
              suppress_decode would still be working for a while.

* [RestGraph] Now for every API call, for example, get/post/fql/url etc,
              you could pass a :secret => true option to indicate that
              for this API call, use secret_access_token instead of the
              normal one.

* [RestGraph] Removed RestGraph#strict option. That is, removed the hack on
              Facebook's response. It's causing problems...

* [RailsUtil] Don't check cookies/session if write_cookies/write_session is
              false. That is avoid accidentally checking unwanted fbs.

* [RailsUtil] Extract rest_graph_filter_uri, which would filter session,
              code, singed_request, etc. on the redirecting URI.

## rest-graph 1.6.0 -- 2010-11-19

* [RestGraph] Added em-http as an alternate for HTTP client. So rest-client
              is no longer a must-have dependency. Pass :async => true to
              RestGraph#get/delete/post/put will pick em-http to do the
              request instead of rest-client. Callback would be the block
              passed to that particular method. There are short hands for
              this, too: use RestGraph#aget/adelete/apost/aput.

              Additionally, callback style also works for rest-client. e.g.

                rg. get('me')                                 # rest-client
                rg. get('me', {}, :async => true){ |result| } # em-http
                rg.aget('me')                    { |result| } # em-http
                rg. get('me')                    { |result| } # rest-client

              So you can always use callback style to ease porting from
              blocking mode (synchronized) to non-blocking mode (evented).

* [RestGraph] Introduced RestGraph#multi to do multiple API call concurrently.
              To use this feature, you'll need to run RestGraph inside an
              EventMachine event loop and install em-http. (And you may want
              async-rack if you're doing Rack application too.)

* [RestGraph] Added a timeout option, which makes rest-graph raise a
              Timeout::Error when any of the API call exceeds the timeout
              limit. This might have some issues in Rubinius 1.1, I'm not
              sure why.

* [RestGraph] Added a log_method option, which would be used for default
              logging template, which is extracted from RailsUtil. So the
              log_method in RailsUtil would be `logger.method(:debug)',
              while in a terminal, you might want `method(:puts)'.

              Previously, you can only use log_handler for logging, which
              you need to write your custom logging template. Now for common
              logging, using log_method is enough. On the other hand,
              log_handler could be used for event handling.

* [RestGraph] Introduced Event::MultiDone and Event::Failed. Those event
              objects would be passed to log_handler and/or log_method.
              The former means a multi-request is done, the latter means
              a request is failed.

* [TestUtil] A collection of tools integrated with RR to ease the pain of
             testing. There are 3 levels of tools to stub the result of
             calling APIs. The highest level is TestUtil.login(1234) which
             would stub a number of results to pretend the user 1234 is
             logged-in.

             The second level are the get/post/put/delete methods for
             TestUtil. For example, to make rg.get('1234') return a
             particular value (such as a hash {'a' => 1}), use
             TestUtil.get('1234'){ {'a' => 1} } to set it up to return
             the specified value (typically a hash).

             The third level is for setting default_data and default_response
             for TestUtil. The default_data is the default value for rg.data,
             which includes the access_token and the user_id (uid). The
             default_response is the response given by any RestGraph API call
             (e.g. get, post) when no explicit response has been defined in
             the second level.

             To use TestUtil, remember to install RR (gem install rr) and
             require 'rest-graph/test_util'. Then put
             RestGraph::TestUtil.setup before any test case starts, and put
             RestGraph::TestUtil.teardown after any test case ends. Setup
             would stub default_data and default_response for you, and
             teardown would remove any stubs on RestGraph. For Rails, you
             might want to put these in test_helper.rb under "setup" and
             "teardown" block, just as the name suggested. For bacon or
             rspec style testing, these can be placed in the "before" and
             "after" blocks.

             In addition, you can get the API calls history via
             RestGraph::TestUtil.history. This would get cleaned up in
             RestGraph::TestUtil.teardown as well.

## rest-graph 1.5.0 -- 2010-10-11

* [RestGraph] Make sure RestGraph::Error#message is string, that way,
              irb could print out error message correctly. Introduced
              RestGraph::Error#error for original error hash. Thanks Bluce.

* [RestGraph] Make RestGraph#inspect honor default attributes, see:
http://groups.google.com/group/rest-graph/browse_thread/thread/7ad5c81fbb0334e8

* [RestGraph] Introduced RestGraph::Error::AccessToken,
                         RestGraph::Error::InvalidAccessToken,
                         RestGraph::Error::MissingAccessToken.
              RestGraph::Error::AccessToken is the parent of the others,
              and RestGraph::Error is the parent of all above.

* [RestGraph] Add RestGraph#next_page and RestGraph#prev_page.
              To get next page for a result from Facebook, example:

                rg.next_page(rg.get('me/feed'))

* [RestGraph] Add RestGraph#for_pages that would crawl from page 1 to
              a number of pages you specified. For example, this might
              crawl down all the feeds:

                rg.for_pages(rg.get('me/feed'), 1000)

* [RestGraph] Added RestGraph#secret_old_rest, see:
http://www.nivas.hr/blog/2010/09/03/facebook-php-sdk-access-token-signing-bug/

If you're getting this error from calling old_rest:

  The method you are calling or the FQL table you are querying cannot be
  called using a session secret or by a desktop application.

Then try secret_old_rest instead. The problem is that the access_token
should be formatted by "#{app_id}|#{secret}" instead of the usual one.

* [RestGraph] Added RestGraph#strict.
              In some API, e.g. admin.getAppProperties, Facebook returns
              broken JSON, which is double encoded, and is not a well-formed
              JSON. That case, we'll need to double parse the JSON to get
              the correct result. For example, Facebook might return this:

                "{\"app_id\":\"123\"}"

              instead of the correct one:

                {"app_id":"123"}

              P.S. This doesn't matter for people who don't use :auto_decode.
              So under non-strict mode, which is the default, rest-graph
              would handle this for you.

* [RestGraph] Fallback to ruby-hmac gem if we have an old openssl lib when
              parsing signed_request which requires HMAC SHA256.
              (e.g. Mac OS 10.5) Thanks Barnabas Debreczeni!

* [RailsUtil] Only rescue RestGraph::Error::AccessToken in controller,
              make other errors raise through.

* [RailsUtil] Remove bad fbs in cookies when doing new authorization.
* [RailsUtil] Make signed_request and session higher priority than
              fbs in cookies. Since Facebook is not deleting bad fbs,
              I guess? Thanks Barnabas Debreczeni!

* [RailsUtil] URI.encode before URI.parse for broken URL Facebook passed.

## rest-graph 1.4.6 -- 2010-09-01

* [RestGraph] Now it will try to pick yajl-ruby or json gem from memory first,
              if it's not there, then try to load one and try to pick one
              again. This way, it won't force you to load two gems at the
              same time if you've installed them both. In addition, there's
              a bug in yajl/json_gem pointed out at:
              http://github.com/brianmario/yajl-ruby/issues/31
              So we're using Nicolas' patch to use yajl directly to workaround
              this issue when we've chosen yajl-ruby json backend.

* [RestGraph] Only cache GET request, don't cache POST/PUT/DELETE

* [RestGrahp] Add RestGraph#lighten and RestGraph#lighten! to remove any
              handler and cache object to make it serializable.

* [RailsUtil] Add ensure_authorized option which enforces the user has
              authorized to the application.

* [RailsUtil] Unified rest_graph_storage_key, which used in cookies/session
              storage, and the key would depend on app_id, just like Facebook
              JavaScript SDK which use fbs_[app_id] as the name of cookie.
              This way, you are able to run different applications with
              different permissions in one Rails application.

* [RailsUtil] Now rest_graph_authorize defaults to do redirect.
              Previously, you'll need to use:
                `rest_graph_authorize(message, true)`
              Now it's:
                `rest_graph_authorize(message)`

## rest-graph 1.4.5 -- 2010-08-07

* [RestGraph] Treat oauth_token as access_token as well. This came from
              Facebook's new signed_request. Why didn't they choose
              consistent name? Why different signature algorithm?

* [RailsUtil] Fixed a bug that didn't reject signed_request in redirect_uri.
              Now code, session, and signed_request are rejected.

* [RailsUtil] Added write_handler and check_handler option to write/check
              fbs with user code, instead of using sessions/cookies.
              That way, you can save fbs into memcache or somewhere.

## rest-graph 1.4.4 -- 2010-08-06

* [RailsUtil] Fixed a bug that empty query appends a question mark,
              that confuses Facebook, so that redirect_uri didn't match.

## rest-graph 1.4.3 -- 2010-08-06

* [RestGraph] Fixed a bug in RestGraph#fbs, which didn't join '&'.
              Thanks, Andrew.

* [RailsUtil] Fixed a bug that wrongly rewrites request URI.
              Previously it is processed by regular expressions,
              now we're using URI.parse to handle this. Closed #4.
              Thanks, Justin.

* [RailsUtil] Favor Request#fullpath over Request#request_uri,
              which came from newer Rack and thus for Rails 3.

## rest-graph 1.4.2 -- 2010-08-05

* [RestGraph] Added RestGraph#fbs to generate fbs with correct sig,
              to be used for future parse_fbs! See the bug in RailsUtil.

* [RailsUtil] Added iframe and write_cookies option.
* [RailsUtil] Fixed a bug that write_session didn't parse because parse_fbs!
              reject the fbs due to missing sig.
* [RailsUtil] Fixed a bug that in Rails 3, must call safe_html to prevent
              unintended HTML escaping. Thanks, Justin.

* Thanks a lot, Andrew.

## rest-graph 1.4.1 -- 2010-08-04

* [RestGraph] Call error_handler when response contains error_code as well,
  which came from FQL response. Thanks Florent.

* [RestGraph] Added RestGraph#parse_signed_request!

* [RestGraph] Added RestGraph#url to generate desired API request URL,
  in case you'll want to use different HTTP client, such as em-http-request,
  or pass the API request to different process of data fetcher.

* [RestGraph] Added an :cache option that allow you to pass a cache
  object, which should respond to [] and []= for reading and writing.
  The cache key would be MD5 hexdigest from the URL being called.
  pass :cache => Rails.cache to rest_graph_setup when using RailsUtil.

* [RailsUtil] Pass :cache => Rails.cache to rest_graph_setup to enable caching.
* [RailsUtil] Favor signed_request over session in rest_graph_setup
* [RailsUtil] Now it's possible to setup all options in rest-graph.yaml.

## rest-graph 1.4.0 -- 2010-07-15

Changes only for RailsUtil, the core (rest-graph.rb) is pretty stable for now.

* Internal code rearrangement.
* Removed url_for helper, it's too hard to do it right.
* Removed @fb_sig_in_canvas hack.
* Added rest_graph method in helper.
* Fixed a bug that logging redirect but not really do direct.
* Now passing :auto_authorize_scope implies :auto_authorize => true.
* Now :canvas option takes the name of canvas, instead of a boolean.
* Now :auto_authorize default to false.
* Now :auto_authorize_scope default to nothing.
* Now there's :write_session option to save fbs in session, default to false.

## rest-graph 1.3.0 -- 2010-06-11

* Now rest-graph is rescuing all exceptions from rest-client.
* Added RestGraph#exchange_sessions to exchange old sessions to access tokens.

* Added RestGraph#old_rest, see:
  http://developers.facebook.com/docs/reference/rest/

* Now all API request accept an additional options argument,
  you may pass :suppress_decode => true to turn off auto-decode this time.
  e.g. rg.get('bad/json', {:query => 'string'}, :suppress_decode => true)
  This is for Facebook who didn't always return JSON in response.

* Renamed fql_server to old_server.
* Favor yaji/json_gem first, then falls back to json, and json_pure.
* Fixed a bug that cookie format from Facebook varies. No idea why.

for RailsUtil:

* Big and fat refactoring in RailsUtil, see example for detail:
  http://github.com/cardinalblue/rest-graph/tree/rest-graph-1.3.0/example
* url_for and link_to would auto pass :host option if it's inside canvas.

## rest-graph 1.2.1 -- 2010-06-02

* Deprecated RailsController, use RailsUtil instead.
* Fixed a bug that passing access_token in query string
  in RestGraph#authorize_url
* Fixed a bug that Facebook changed the format (I think) of fbs_ in cookies.
  Thanks betelgeuse, closes #1
  http://github.com/cardinalblue/rest-graph/issues/issue/1

## rest-graph 1.2.0 -- 2010-05-27

* Add RestGraph#parse_json!
* Add RailsController to help you integrate into Rails.
* Simplify arguments checking and require dependency.
* Now if there's no secret in RestGraph, sig check would always fail.
* Now there's a Rails example.
  http://github.com/cardinalblue/rest-graph/tree/master/example

* Add error_handler option. Default behavior is raising ::RestGraph::Error.
  You may want to pass your private controller method to do redirection.
  Extracted from README:
  # You may want to do redirect instead of raising exception, for example,
  # in a Rails application, you might have this private controller method:
  def redirect_to_authorize error = nil
    redirect_to @rg.authorize_url(:redirect_uri => request.url)
  end

  # and you'll use that private method to do error handling:
  def setup_rest_graph
    @rg = RestGraph.new(:error_handler => method(:redirect_to_authorize))
  end

* Add log_handler option. Default behavior is do nothing.
  You may want to do this in Rails:
  RestGraph.new(:log_hanlder => lambda{ |duration, url|
                                  Rails.logger.debug("RestGraph "         \
                                                     "spent #{duration} " \
                                                     "requesting #{url}")
                                })

* Add RestGraph#fql_multi to do FQL multiquery. Thanks Ethan Czahor
  Usage: rg.fql_multi(:query1 => 'SELECT ...', :query2 => 'SELECT ...')

## rest-graph 1.1.1 -- 2010-05-21

* Add oauth realted utilites -- authorize_url and authorize!
* Fixed a bug that in Ruby 1.8.7-, nil =~ /regexp/ equals to false.
  It is nil as expected in Ruby 1.9.1+

## rest-graph 1.1.0 -- 2010-05-13

* Main repository was moved to http://github.com/cardinalblue/rest-graph
  Sorry for the inconvenience. I'll keep pushing to both repositories until
  I am too lazy to do that.

* Better way to deal with default attributes, use class methods.

* If you want to auto load config, do require 'rest-graph/auto_load'
  if it's rails, it would load the config from config/rest-graph.y(a)ml.
  if you're using rails plugin, we do require 'rest-graph/auto_load'
  for you.

* Config could be loaded manually as well. require 'rest-graph/load_config'
  and RestGraph::LoadConfig.load_config!('path/to/rest-graph.yaml', 'env')

## rest-graph 1.0.0 -- 2010-05-06

* now access_token is saved in data attributes.
* cookies related methods got renamed, and saved all data in RestGraph
* parse failed would return nil, while data is always a hash

## rest-graph 0.9.0 -- 2010-05-04

* renamed :server option to :graph_server
* added :fql_server option and fql support.
* cookies related parsing utility is now instance methods.
  you'll need to pass app_id and secret when initializing
* if sig in cookies is bad, then it won't extract the access_token

## rest-graph 0.8.1 -- 2010-05-03

* added access_token parsing utility

## rest-graph 0.8.0 -- 2010-05-03

* release early, release often
