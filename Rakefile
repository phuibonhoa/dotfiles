require 'rake'
require 'erb'

FILES_NOT_TO_LINK = %w[Rakefile README.rdoc LICENSE]

desc "install the dot files into user's home directory"
task :install do
  
  skip_all = false
  overwrite_all = false
  backup_all = false
  
  
  Dir['*'].each do |file|
    next if FILES_NOT_TO_LINK.include?(file)
    
    skip = false
    overwrite = false
    backup = false
    
    target = target_path(file)
    
    if File.exists?(target) || File.symlink?(target)
      if File.identical?(file, target)
        puts "Skipping #{target}.  Identical file already exists."
        skip = true
      elsif !(skip_all || overwrite_all || backup_all)
        puts "File already exists: #{target}, what do you want to do? [s]kip, [S]kip all, [o]verwrite, [O]verwrite all, [b]ackup, [B]ackup all"
        case STDIN.gets.chomp
        when 'o' then overwrite = true
        when 'b' then backup = true
        when 'O' then overwrite_all = true
        when 'B' then backup_all = true
        when 'S' then skip_all = true
        when 's' then skip = true
        end
      end

      remove(target) if overwrite || overwrite_all
      backup(target) if backup || backup_all   
      link_file(file) unless skip || skip_all
    else
      link_file(file) unless skip_all
    end
  end
end

def target_path(file)
  File.join(ENV['HOME'], ".#{file.sub('.erb', '')}")
end

def backup(file)
  `mv #{file} #{file}.backup`
  puts "Backing up #{file}"
end

def remove(file)
  FileUtils.rm_rf(file)
  puts "Deleting #{file}"
end

def link_file(file)
  target = target_path(file)
  if file =~ /.erb$/
    puts "Generating #{target}"
    File.open(target, 'w') do |new_file|
      new_file.write ERB.new(File.read(file)).result(binding)
    end
  else
    puts "Linking #{target}"
    system %Q{ln -s "$PWD/#{file}" "#{target}"}
  end
end
