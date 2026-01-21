# Render Deployment Guide for ZaqenCare Backend

## Prerequisites
- A [Render account](https://render.com) (free tier available)
- Your Supabase credentials
- Gmail app password for email functionality

## Deployment Steps

### Option 1: Deploy via Render Blueprint (Recommended)

1. **Push to GitHub** (if not already done):
   ```bash
   git add .
   git commit -m "Prepare for Render deployment"
   git push origin main
   ```

2. **Create New Web Service on Render**:
   - Go to [Render Dashboard](https://dashboard.render.com/)
   - Click "New +" → "Blueprint"
   - Connect your GitHub repository
   - Render will automatically detect the `render.yaml` file

3. **Configure Environment Variables**:
   After the blueprint is applied, go to your web service and add these environment variables:
   - `SUPABASE_URL`: Your Supabase project URL
   - `SUPABASE_KEY`: Your Supabase service role key
   - `EMAIL_PASSWORD`: Your Gmail app password
   - `SECRET_KEY`: (automatically generated or set your own)

### Option 2: Manual Deployment

1. **Push to GitHub** (if not already done)

2. **Create New Web Service**:
   - Go to Render Dashboard → "New +" → "Web Service"
   - Connect your GitHub repository
   - Select the repository

3. **Configure Service Settings**:
   - **Name**: `zaqencare-backend` (or your preferred name)
   - **Region**: Choose closest to your users
   - **Branch**: `main`
   - **Runtime**: `Python 3`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn schedule:app`

4. **Add Environment Variables**:
   - `PYTHON_VERSION`: `3.11.0`
   - `SUPABASE_URL`: Your Supabase project URL
   - `SUPABASE_KEY`: Your Supabase service role key (NOT the anon key)
   - `EMAIL_PASSWORD`: Your Gmail app password
   - `SECRET_KEY`: Generate a secure random string

5. **Deploy**: Click "Create Web Service"

## Important Configuration Notes

### Environment Variables
Make sure to use the **service_role** key from Supabase, not the anon key. You can find it in:
- Supabase Dashboard → Project Settings → API → service_role key

### Gmail App Password
To get a Gmail app password:
1. Go to your Google Account settings
2. Security → 2-Step Verification (enable if not already)
3. App passwords → Generate new app password
4. Use this password in the `EMAIL_PASSWORD` environment variable

### CORS Configuration
The app is configured with CORS enabled. If you need to restrict origins in production:
- Update the CORS configuration in `schedule.py`
- Example: `CORS(app, origins=["https://yourfrontend.com"])`

## Post-Deployment

1. **Test Your Endpoints**:
   Your service will be available at: `https://your-service-name.onrender.com`
   
   Test the dashboard stats endpoint:
   ```bash
   curl https://your-service-name.onrender.com/dashboard/stats
   ```

2. **Monitor Logs**:
   - Go to your service on Render Dashboard
   - Click "Logs" to view real-time logs
   - Check for any errors or issues

3. **Update Frontend**:
   Update your frontend/mobile app to use the new Render URL

## Troubleshooting

### Service Won't Start
- Check the logs in Render Dashboard
- Verify all environment variables are set correctly
- Ensure `requirements.txt` has all dependencies

### Database Connection Issues
- Verify `SUPABASE_URL` and `SUPABASE_KEY` are correct
- Make sure you're using the service_role key, not anon key
- Check Supabase dashboard for any connection limits

### Email Not Sending
- Verify `EMAIL_PASSWORD` is the app password, not your regular Gmail password
- Check Gmail security settings
- Review logs for specific SMTP errors

## Free Tier Limitations

Render's free tier includes:
- 750 hours/month of running time
- Services spin down after 15 minutes of inactivity
- First request after spin-down may take 30-60 seconds (cold start)

To avoid cold starts, consider:
- Upgrading to a paid plan
- Using an uptime monitoring service to ping your API periodically

## Scaling

When you're ready to scale:
- Upgrade to a paid plan for always-on instances
- Consider horizontal scaling with multiple instances
- Add Redis for session management and caching
- Set up a CDN for static assets

## Support

For issues specific to Render deployment:
- [Render Documentation](https://render.com/docs)
- [Render Community Forum](https://community.render.com/)

For application issues, check your application logs and Supabase dashboard.
