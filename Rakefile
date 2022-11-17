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
  Emoji.create(key) do |char|
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
    name = [
      emoji.raw,
      emoji.description,
      emoji.aliases.map { "\"#{_1}\"" }.join(", "),
    ]
    name << "(#{emoji.tags.join(", ")})" unless emoji.tags.empty?

    File.write("build/#{emoji.name} [#{uuid}].json", JSON.dump({
      alfredsnippet: {
        snippet: emoji.raw,
        dontautoexpand: true,
        uid: uuid,
        name: name.join(" "),
        keyword: emoji.name,
      },
    }))
  end
end
task :default => :rebuild
