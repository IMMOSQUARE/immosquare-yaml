require "json"
require "rake"
require "immosquare-yaml"

namespace :immosquare_yaml do
  
  desc "Clean, parse, dump, translate the sample files"
  namespace :sample do
    ##=============================================================##
    ## Load config keys from config_dev.yml
    ##=============================================================##
    def load_config
      path = "#{File.dirname(__FILE__)}/config_dev.yml"
      abort("Error: config_dev.yml not found") if !File.exist?(path)
      
      ##=============================================================##
      ## Load config keys from config_dev.yml
      ##=============================================================##
      dev_config = ImmosquareYaml.parse(path)
      abort("Error config_dev.yml is empty") if dev_config.nil?

      ImmosquareYaml.config do |config|
        config.openai_api_key = dev_config["openai_api_key"]
      end
    end

    ##=============================================================##
    ## Clean the sample YAML file                                  
    ##=============================================================##
    desc "Clean the sample files"
    task :clean do
      input  = "spec/fixtures/sample.en.yml"
      output = "spec/output/sample_cleaned.en.yml"
      ImmosquareYaml.clean(input, :output => output)
    end

    ##=============================================================##
    ## Parse the sample YAML file                                  
    ##=============================================================##
    desc "Parse the sample files"
    task :parse do
      data = ImmosquareYaml.parse("spec/fixtures/sample.en.yml")
      puts JSON.pretty_generate(data)
      File.write("spec/fixtures/sample.json", JSON.pretty_generate(data))
    end

    ##=============================================================##
    ## Dump the sample YAML file                                  
    ##=============================================================##
    desc "Dump the sample files"
    task :dump do
      data = JSON.parse(File.read("spec/fixtures/sample.json"))
      yaml = ImmosquareYaml.dump(data)
      puts yaml
    end


    ##=============================================================##
    ## Translate the sample YAML file
    ##=============================================================##
    desc "Translate the sample files"
    task :translate do
      load_config
      input = "spec/output/sample_cleaned.en.yml"
      ImmosquareYaml::Translate.translate(input, "fr")
    end
  end


  namespace :app do
    ##============================================================##
    ## Function to translate translation files in rails app
    ##============================================================##
    task :translate => :environment do
      source_locale  = "fr"
      locales        = I18n.available_locales.map(&:to_s).reject {|l| l == source_locale }
      Dir.glob("#{Rails.root}/config/locales/**/*#{source_locale}.yml").each do |file|
        locales.each do |locale|
          ImmosquareYaml::Translate.translate(file, locale) 
        end
      end
    end

    ##============================================================##
    ## Function to clean translation files in rails app
    ##============================================================##
    desc "Clean translation files in rails app"
    task :clean => :environment do
      Dir.glob("#{Rails.root}/config/locales/**/*.yml").each do |file|
        ImmosquareYaml.clean(file)
      end
    end
  end
end
