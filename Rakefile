desc "deploy to www.clovescarneirojr.com"
task :deploy do
  require 'rubygems'
  require 'right_aws'
  
  BUCKET_NAME = 'clovescarneirojr'
  DISTRIBUTION_ID = 'E2GUKSN5IUZCUQ'
  # set when deploying
  ACCESS_KEY = ''
  SECRET_KEY = ''

  # copy all Jekyll generated files to S3
  s3 = RightAws::S3Interface.new ACCESS_KEY, SECRET_KEY
  bucket_acl = s3.get_acl(BUCKET_NAME)
  paths_to_invalidate = []
  
  Dir["_site/**/*.*"].each do |file| 
    file_path = File.join(File.dirname(__FILE__), file)
    s3_file_name = file.gsub('_site/', '')
    
    puts "-- Uploading #{file_path} to S3 as #{s3_file_name} --"
    s3.put(BUCKET_NAME, s3_file_name,  File.open(file_path))
    s3.put_acl(BUCKET_NAME, s3_file_name, bucket_acl[:object])
    paths_to_invalidate << "/#{s3_file_name}"
  end
  
  # now, we need to invalidate the distribution
  acf = RightAws::AcfInterface.new ACCESS_KEY, SECRET_KEY
  puts "-- Invalidating the CloudFront distribution --"
  acf.create_invalidation DISTRIBUTION_ID, :path => paths_to_invalidate
end
