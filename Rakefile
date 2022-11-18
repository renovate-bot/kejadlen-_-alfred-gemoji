require "json"
require "securerandom"

require "gemoji"

# https://github.com/PurpleBooth/alfred-emoji-snippet-pack/blob/main/bin/generate

{
  light_skin_tone: ["🏻", "Light Skin Tone"],
  medium_light_skin_tone: ["🏼", "Medium-Light Skin Tone"],
  medium_skin_tone: ["🏽", "Medium Skin Tone"],
  medium_dark_skin_tone: ["🏾", "Medium-Dark Skin Tone"],
  dark_skin_tone: ["🏿", "Dark Skin Tone"],
}.each do |key, (unicode_alias, desc)|
  Emoji.create(key.to_s) do |char|
    char.add_unicode_alias unicode_alias
    char.description = desc
  end
end

file "build" do
  mkdir "build"
end

task :rebuild => "build" do
  rm Dir["build/*.json"]

  Emoji.all.each do |emoji|
    uuid = SecureRandom.uuid
    keywords = [emoji.description]

    keywords.concat(
      emoji.aliases
        .map { _1.gsub(?_, " ") }
        .reject { str_match(emoji.description, _1) }
    )
    keywords.concat(emoji.tags)

    File.write("build/#{emoji.name} [#{uuid}].json", JSON.dump({
      alfredsnippet: {
        snippet: emoji.raw,
        dontautoexpand: true,
        uid: uuid,
        name: "#{emoji.raw} #{keywords.join(", ")}",
        keyword: emoji.name,
      },
    }))
  end
end
task :default => :rebuild

def str_match(a, b)
  return true if a.gsub(/[^\w]+/, "").downcase.include?(b.gsub(/[^\w]+/, "").downcase)
  levenshtein(a, b) < 2
end

def levenshtein(a, b)
  memo = Hash.new {|h,(a,b)|
    h[[a,b]] = (->() {
      return b.length if a.empty?
      return a.length if b.empty?
      return h[[a[1..], b[1..]]] if a[0] == b[0]

      1 + [
        h[[a, b[1..]]],
        h[[a[1..], b]],
        h[[a[1..], b[1..]]],
      ].min
    }).()
  }

  memo[[a, b].map(&:downcase)]
end
