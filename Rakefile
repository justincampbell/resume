require 'bundler'
Bundler.require

markdown = Dir['*.md'].first
basename = File.basename(markdown, '.md')
pdf = "#{basename}.pdf"
css = "#{basename}.css"

task default: pdf

desc "Build #{pdf} from #{markdown}"
file pdf => [markdown, css] do
  puts "Building #{pdf}"

  `gimli -file #{markdown} -stylesheet #{css}`
end

desc "Upload to S3"
task upload: pdf do
  verify_env_variables

  puts "Uploading #{pdf} to #{fog_directory}"

  body = File.open(pdf)

  bucket = s3.directories.create(key: fog_directory, public: true)
  bucket.files.create(key: pdf, body: body, public: true)
end

def fog_directory
  ENV['FOG_DIRECTORY']
end

def s3
  @s3 ||= Fog::Storage.new(
    provider: :aws,
    aws_access_key_id: ENV['AWS_ACCESS_KEY'],
    aws_secret_access_key: ENV['AWS_SECRET_KEY']
  )
end

def verify_env_variables
  %w[
    AWS_ACCESS_KEY
    AWS_SECRET_KEY
    FOG_DIRECTORY
  ].each do |env_variable|
    unless ENV[env_variable]
      puts "#{env_variable} required"
      exit 1
    end
  end
end
