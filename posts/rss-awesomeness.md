::title::       RSS Awesomeness, Creating feeds with Sinatra & Builder
::published::   2010-07-26
::tags::        ruby,programming

So I was reading "the Design Monkey blog":http://designmonkey.blogspot.com this evening and suddenly a vast sense of inadequacy welled up inside me. Was it because I had such vastly inferior design skills? Or maybe far less comical writing ability?

Hell No.

The design monkey provides that most awesome of things (as I'm sure do many others), an RSS feed of his eloquent prose, so not to be outdone and armed with the best of tools, I set out creating one with Ruby, [builder](http://builder.rubyforge.org/) and [Sinatra](http://www.sinatrarb.com/)

This turned out to take about 10 minutes and be amazingly less impressive than it sounds but here goes anyway. First you should create a builder template for your RSS feed. Mine looks like this: 

<pre class="brush: ruby">
  xml.instruct! :xml, :version => "1.0"
  xml.rss :version => "2.0" do
    xml.channel do
      xml.title "eightbitraptor"
      xml.description "The personal blog of developer, music lover and recovering sysadmin Matt House"
      xml.link "http://eightbitraptor.com/posts"

      for post in locals[:posts]
        xml.item do
          xml.title post.title
          xml.description to_html(post.body)
          xml.pubDate pretty_date(post.published)
          xml.link post_url(post)
        end
      end
    end
  end
</pre>

This is saved in my views folder as @feed.builder@ so that in my main Application I can define my route like so:

<pre class="brush: ruby">
  get '/feed.xml' do
    builder :feed, :locals => { :posts => Post.all }
  end
</pre>

Where a Post class does all the magical awesome (but is essentially just a way of grouping together titles and bodies as referenced in the builder template).

And that is pretty much all you need to do to generate functional rss feeds with Sinatra. If you want to dig deeper, especially into what my mystical Post object is, then the code is on github as per usual.

<a class="github-project ruby" href="http://github.com/eightbitraptor/eightbitraptor.com/tree/master"><span>Get the source</span></a>
