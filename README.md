# Create-Activity-Inside-TimeLine

## For Specific User: From Portals Button Click : Inside Approval Request

 @http.route('/my/tax_certificate', type='http', auth="user", website=True)
    def portal_my_req_tax_certificate(self, **kw):
        # Find or create the category for this approval request
        category = request.env['approval.category'].search([('name', '=', 'Tax Certificate Approvals')], limit=1)
        if not category:
            # Optional: Create a new category if not found
            category = request.env['approval.category'].create({
                'name': 'Tax Certificate Approvals',
            })

        # Create the approval request with the required category_id
        approval_request = request.env['approval.request'].create({
            'name': 'Tax Certificate Approval',  # Name of the approval request
            'request_owner_id': request.env.user.id,  # Assign to the current portal user
            'category_id': category.id,  # Required field: Category ID
        })

        # Schedule an activity for the created approval request
        approval_request.activity_schedule(
            activity_type_id=request.env.ref('mail.mail_activity_data_todo').id,
            summary="Approval Required..!",
            user_id=26,  # Assign the activity to a specific user (e.g., User ID 2)
            note="Please review and approve this Tax Certificate request.",
            date_deadline=fields.Date.today(),
        )

        # Render the portal view
        return request.render("sh_portal_dashboard.portal_my_req_salary_slip")



## Inside Contact:

# @http.route('/my/tax_certificate', type='http', auth="user", website=True)
    # def portal_my_req_tax_certificate(self, **kw):
    #     # Get the partner linked to user ID 26
    #     user = request.env['res.users'].browse(2)
    #     partner = user.partner_id
    #
    #     if partner:
    #         partner.activity_schedule(
    #             activity_type_id=request.env.ref('mail.mail_activity_data_todo').id,
    #             summary="Approval Required..!",
    #             user_id=2,  # Assign the activity to user ID 26
    #             note="Please review and approve this Tax Certificate request.",
    #             date_deadline=fields.Date.today(),
    #         )
    #
    #     # Render the portal view
    #     return request.render("sh_portal_dashboard.portal_my_req_salary_slip")




## From Models:

    def action_create_activity(self):
        for x in self:
            # x.message_post(subject="Approval Request", body='Please Approve the Payment for further proceed.',
            #                partner_ids=[2], subtype_id=self.env.ref('mail.act').id)
            #---> send payment to clock icon,timeline for a reminder , use this code
            x.activity_schedule(activity_type_id=self.env.ref('mail.mail_activity_data_todo').id, summary="Approval Required..!", user_id = 2, note="Please review and approve this Record", date_deadline=fields.Date.today())
            x.state = 'submit_approval'


 
