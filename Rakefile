require 'bundler'
Bundler.require

markdown = Dir['*.md'].first
basename = File.basename(markdown, '.md')
pdf = "#{basename}.pdf"
css = "#{basename}.css"

task default: pdf

desc "Build #{pdf} from #{markdown}"
file pdf => [markdown, css] do
  `gimli -file #{markdown} -stylesheet #{css}`
end

desc "Upload to S3"
task upload: pdf do
  body = File.open(pdf)

  bucket = s3.directories.create(key: ENV['FOG_DIRECTORY'], public: true)
  bucket.files.create(key: pdf, body: body, public: true)
end

def s3
  @s3 ||= Fog::Storage.new(
    provider: :aws,
    aws_access_key_id: ENV['AWS_ACCESS_KEY'],
    aws_secret_access_key: ENV['AWS_SECRET_KEY']
  )
end
