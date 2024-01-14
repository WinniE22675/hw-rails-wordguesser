# WordGuesser on Rails

In a [previous assignment](https://github.com/saasbook/hw-sinatra-saas-wordguesser) you created a simple Web app that plays the Wordguesser game.

More specifically:

1. You wrote the app's code in its own class, `WordGuesserGame`, which knows nothing about being part of a Web app.

2. You used the Sinatra framework to "wrap" the game code by providing a set of RESTful actions that the player can take, with the following routes:

    * `GET  /new`-- default ("home") screen that allows player to start new game
    * `POST /create` -- actually creates the new game
    * `GET  /show` -- show current game status and let player enter a move
    * `POST /guess` -- player submits a letter guess
    * `GET  /win`   -- redirected here when `show` action detects game won
    * `GET  /lose`  -- redirected here when `show` action detects game lost

3. To maintain the state of the game between (stateless) HTTP requests, you stored a copy of the `WordGuesserGame` instance itself in the `session[]` hash provided by Sinatra, which is an abstraction for storing information in cookies passed back and forth between the app and the player's browser.

In this assignment, you'll reuse the *same* game code but "wrap" it in a simple Rails app instead of Sinatra.

## Learning Goals

Understand the differences between how Rails and Sinatra handle various aspects of constructing SaaS, including: 

* how routes are defined and mapped to actions; 
* the directory structure used by each framework;
* how an app is started and stopped; 
* how the app's behavior can be inspected by looking at logs or invoking a debugger. 

## 1. Run the App

**NOTE: You may find these [Rails guides](http://guides.rubyonrails.org/v4.2/) and the [Rails reference documentation](http://api.rubyonrails.org/v4.2.9/) helpful to have on hand.**

Like substantially all Rails apps, you can get this one running by doing these steps:

1. Clone or fork the [repo](https://github.com/saasbook/hw-rails-wordguesser)

2. Change into the app's root directory `hw-rails-wordguesser`

3. Run `bundle install --without production`

4. | Local Development                      	| Codio                                                     	|
    |----------------------------------------	|-----------------------------------------------------------	|
    | Run `rails server` to start the server 	| Run <br>`rails server -b 0.0.0.0`<br> to start the server 	|

### To view your site in Codio
Use the Preview button that says "Project Index" in the top tool bar. Click the drop down and select "Box URL" 

![.guides/img/BoxURLpreview](https://global.codio.com/content/BoxURLpreview.png)

For subsequent previews, you will not need to press the drop down -- your button should now read "Box URL".

**Q1.1.**  What is the goal of running `bundle install`?
# resolve dependencies and install all needed gems
# แก้ไข dependencies และติดตั้งอัญมณีที่จำเป็นทั้งหมด

**Q1.2.**  Why is it good practice to specify `--without production` when running  it on your development computer?
# option prevents the local installation of any production gem means whatever gems are in the production group will not be installed
# ตัวเลือกป้องกันการติดตั้งของ gem ในเครื่อง gem ใน production group จะไม่ถูกติดตั้ง

**Q1.3.** 
(For most Rails apps you'd also have to create and seed the development database, but like the Sinatra app, this app doesn't use a database at all.)

Play around with the game to convince yourself it works the same as the Sinatra version.
# ทำงานได้เหมือนกัน

## 2. Where Things Are

Both apps have similar structure: the user triggers an action on a game via an HTTP request; a particular chunk of code is called to "handle" the request as appropriate; the `WordGuesserGame` class logic is called to handle the action; and usually, a view is rendered to show the result.  But the locations of the code corresponding to each of these tasks is slightly different between Sinatra and Rails.

**Q2.1.** Where in the Rails app directory structure is the code corresponding to the `WordGuesserGame` model?
# `app/model/word_guesser_game.rb`

**Q2.2.** In what file is the code that most closely corresponds to the  logic in the Sinatra apps' `app.rb` file that handles incoming user actions?
# `app/controllers/game_controller.rb`

**Q2.3.** What class contains that code?
# Class GameController

**Q2.4.** From what other class (which is part of the Rails framework) does that class inherit? 
# inherit from ApplicationController

**Q2.5.** In what directory is the code corresponding to the Sinatra app's views (`new.erb`, `show.erb`, etc.)?  
# new.erb => new.html.erb,  show.erb => show.html.erb, etc.

**Q2.6.** The filename suffixes for these views are different in Rails than they were in the Sinatra app.  What information does the rightmost suffix of the filename  (e.g.: in `foobar.abc.xyz`, the suffix `.xyz`) tell you about the file contents?  
# .erb => ERB (Embedded Ruby) is a templating system built into Ruby that allows you to embed Ruby code directly into your text files
# .erb => ERB (Embedded Ruby) เป็นระบบเทมเพลตที่สร้างไว้ใน Ruby ซึ่งช่วยให้สามารถฝังโค้ด Ruby ลงในไฟล์ข้อความได้โดยตรง

**Q2.7.** What information does the  other suffix tell you about what Rails is being asked to do with the file?
# .html.erb file is HTML with Ruby code embedded in. Rails will evaluate the Ruby to add content to the file dynamically, and will output a "pure" HTML file for rendering.
# ไฟล์ .html.erb เป็นไฟล์ HTML ที่มีโค้ด Ruby ฝังอยู่ โดย Rails จะประเมิน Ruby เพื่อเพิ่มเนื้อหาลงในไฟล์แบบไดนามิก และจะส่งออกไฟล์ HTML "บริสุทธิ์" สำหรับการเรนเดอร์

**Q2.8.** In what file is the information in the Rails app that maps routes (e.g. `GET /new`)  to controller actions?
#  `GET  /new`    => 'game#new',   :as => 'new_game'
#  -- default ("home") screen that allows player to start new game

#  `POST /create` => 'game#create',:as => 'create_game'
#  -- actually creates the new game

#  `GET  /show`   => 'game#show',  :as => 'game'
#  -- show current game status and let player enter a move

#  `POST /guess`  => 'game#guess', :as => 'guess'
#  -- player submits a letter guess

#  `GET  /win`    => 'game#win',   :as => 'win_game'
#  -- redirected here when `show` action detects game won

#  `GET  /lose`   => 'game#lose',  :as => 'lose_game'  
#  -- redirected here when `show` action detects game lost

**Q2.9.** What is the role of the `:as => 'name'` option in the route declarations of `config/routes.rb`?  (Hint: look at the views.)
# set path เพื่อเมื่อ redirect_to xxx_path ก็จะเรียก maps routes ของ path นั้น ๆ (e.g. `GET /show` => 'game#show',  :as => 'game' แล้ว redirect_to game_path ก็จะ `GET /show` ไปทำฟังก์ชั่น show ใน `app/controllers/game_controller.rb`)

## 3. Session

Both apps ensure that the current game is loaded from the session before any controller action occurs, and that the (possibly modified) current game is replaced in the session after each action completes.

**Q3.1.** In the Sinatra version, `before do...end` and `after do...end` blocks are used for session management.  What is the closest equivalent in this Rails app, and in what file do we find the code that does it?
# before do...end => before_action :get_game_from_session
# after do...end => after_action  :store_game_in_session
#
# find the code in app/controllers/game_controller.rb

**Q3.2.** A popular serialization format for exchanging data between Web apps is [JSON](https://en.wikipedia.org/wiki/JSON).  Why wouldn't it work to use JSON instead of YAML?  (Hint: try replacing `YAML.load()` with `JSON.parse()` and `.to_yaml` with `.to_json` to do this test.  You will have to clear out your cookies associated with `localhost:3000`, or restart your browser with a new Incognito/Private Browsing window, in order to clear out the `session[]`.  Based on the error messages you get when trying to use JSON serialization, you should be able to explain why YAML serialization works in this case but JSON doesn't.)
# ไฟล์ JSON ไม่สามารถใช้แทน YAMLได้

## 4. Views

**Q4.1.** In the Sinatra version, each controller action ends with either `redirect` (which as you can see becomes `redirect_to` in Rails) to redirect the player to another action, or `erb` to render a view.  Why are there no explicit calls corresponding to `erb` in the Rails version? (Hint: Based on the code in the app, can you discern the Convention-over-Configuration rule that is at work here?)
# เพราะใน rails ใช้ Convention-over-Configuration ซึ่งมี Directory Structure ที่ชัดเจน ตรงกับ Convention ที่กำหนดไว้

**Q4.2.** In the Sinatra version, we directly coded an HTML form using the `<form>` tag, whereas in the Rails version we are using a Rails method `form_tag`, even though it would be perfectly legal to use raw HTML `<form>` tags in Rails.  Can you think of a reason Rails might introduce this "level of indirection"?
# Rails method `form_tag` สามารถปรับและนำไปใช้งานได้หลากหลายกว่า HTML `<form>`

**Q4.3.** How are form elements such as text fields and buttons handled in Rails?  (Again, raw HTML would be legal, but what's the motivation behind the way Rails does it?)
# ตาม Convention ที่กำหนดไว้ การตั้งชื่อ inputs ในการการเชื่อมต่อ (เช่น input type="text" name="guess" autocomplete="off" class="form-control col-md-1"/>)

**Q4.4.** In the Sinatra version, the `show`, `win` and `lose` views re-use the code in the `new` view that offers a button for starting a new game. What Rails mechanism allows those views to be re-used in the Rails version?
# use render partial view  
# <%= render :template => 'game/new' %>

## 5. Cucumber scenarios

The Cucumber scenarios and step definitions (everything under `features/`, including the support for Webmock in `webmock.rb`) was copied verbatim from the Sinatra version, with one exception: the `features/support/env.rb` file is simpler because the `cucumber-rails` gem automatically does some of the things we had to do explicitly in that file for the Sinatra version.

Verify the Cucumber scenarios run and pass by running `rake cucumber`.

**Q5.1.** What is a qualitative explanation for why the Cucumber scenarios and step definitions didn't need to be modified at all to work equally well with the Sinatra or Rails versions of the app?
# เนื่องจากCucumber เน้นไปที่การทำtest case แบบ BDD(behavior Drive Development)จากEnd User ทำให้ไม่ต้องการการปรับแต่งในการใช้งานทั้ง Sinitra และ Rails