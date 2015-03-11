---
layout: post
title: "Octopress Drafts"
date: 2012-05-28 11:58
comments: true
categories: 
- Blogging
---
I usually have several blog-post ideas in the pipeline, which I want to keep separate from my finished posts. This article shows how I added some `rake` tasks to do this.

What I’ve tried
----
* Octopress’ `published: false` does not work for me. It still generates the drafts, publishing them prematurely on the blog. I don’t know why this is. But even if it worked, I would still have my unfinished drafts somewhere among all my published posts, in the same folder.
* I tried writing the drafts in a separate folder on the file system, and only when I was done I would do `rake new_post["My finished post"]`, open the Markdown file, and paste in the finished writing.

What I do now
-----
1. Create the draft:
	
	    $ rake new_draft["Octopress Drafts"]
	    Created draft at source/_drafts/2012-05-28-octopress-drafts.md
2. The `new_draft` task opens it in Byword, so I can start writing right away.
3. When I’m ready:
	
	    $ rake promote_draft
	      [0]  2012-05-22-weekly-picks-11.md
	      [1]  2012-05-26-party-profile.md
	      [2]  2012-05-26-uberfordert.md
	      [3]  2012-05-28-octopress-drafts.md
	    Promote which draft? [0-9] 3
	    Promoted to source/_posts/2012-05-28-octopress-drafts.md

Rakefile additions
-----
Here are my Rakefile additions if you want the same for yourself:

	# -- Misc Configs -- ##
	drafts_dir      = "_drafts"   # directory for unfinished blog files not to be generated
	
	desc "Begin a new draft in #{source_dir}/#{drafts_dir}"
	task :new_draft, :title do |t, args|
	  filename = `rake new_post['#{args.title}']`
	  posts_path = "#{source_dir}/#{posts_dir}"
	  drafts_path = "#{source_dir}/#{drafts_dir}"
	  mkdir_p drafts_path
	  destination = "#{drafts_path}/#{filename.strip}"
	  FileUtils.mv("#{posts_path}/#{filename.strip}", destination)
	  puts "Created draft at #{destination}"
	  `open #{destination}`
	end
	
	desc "Move a draft to #{source_dir}/#{posts_dir} when you're ready"
	task :promote_draft do
	  drafts_path = "#{source_dir}/#{drafts_dir}"
	  Dir.glob("#{drafts_path}/*.*").each_with_index do |draft, idx|
	    draft_shortname = draft.gsub(/#{drafts_path}\//, '')
	    puts "  [#{idx}]  #{draft_shortname}"
	  end
	  
	  index_to_promote = ask("Promote which draft?", ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9'])
	  drafts_path = "#{source_dir}/#{drafts_dir}"
	  source = Dir.glob("#{drafts_path}/*.*")[index_to_promote.to_i]
	  filename = source.gsub(/#{drafts_path}\//, '')
	  destination = "#{source_dir}/#{posts_dir}/#{filename}"
	  FileUtils.mv(source, destination)
	  puts "Promoted to #{destination}"
	end
	
	# task :new_post  // changes:
	  basename = "#{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}.#{new_post_ext}"
	  filename = "#{source_dir}/#{posts_dir}/#{basename}"
	  
	  # ... and at the very end:
	  puts basename   # e.g. 2012-05-28-octopress-drafts.md
	# end

Let me know what you think on Twitter: [@yangmeyer](https://twitter.com/#!/yangmeyer)

Cody quality
-----
I’m afraid to say that my Ruby has become rather rusty, to the extent that I am happy that my code works (it’s scarily reminiscent of my JavaScript “skills” back in 2001, actually).

Some things I would like to improve:

* I originally had a `list_drafts` task, which I wanted to invoke from `promote_draft`. Without an Internet connection here, I could not find out how to do this.
* Obviously, there is a lot of `"#{source_dir}/#{drafts_dir}"` code duplication going on. I probably should extract it to `drafts_path`?
* I lazily hard-coded the valid values of which draft to promote, as stringified integers 0–9. What would be a nice way to read in any integer?
* This being a blogging engine for hackers, who should know what they’re doing, I also don’t check whether the index is valid.

I’d be happy to hear your suggestions. Always glad to learn.